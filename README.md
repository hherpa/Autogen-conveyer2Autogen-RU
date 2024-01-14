# Содержание
требуемые обновления autogen:

[1. Autogen-conveyer](#Autogen_conveyer)

[2. класс агентов ClI_Agent](#ClI_Agent)

[2.1 Два варианта реализаци класса CLI_Agent](#ClI_Agent_2)

## Autogen-conveyer и его отделы:
<a name="Autogen_conveyer"></a> 
1. Что это и зачем это нужно? (* Autogen-conveyer - это новый подход к....)
2. из чего состоит конвейер (отделы, подотделы)
3. основная проблема: парсинг результата отдела (+ наша идея с агентов что будет помечать результат чата тегом @final)
4. pipeline
## класс агентов ClI_Agent
<a name="ClI_Agent"></a> 
CLI_Agent - это класс агентов для постоянного анализа группового чата autogen и выполнения определенных функций при срабатывании условий. Эти фукнции деляться на два типа: `&command_name` и `!edit_memory`.
В памяти агента класса CLI_Agent может содержаться информация, которая должна считаться истинной для чата, в котором он участвует. Агент данного класса может использовать команду в формате ```!edit_memory <старое значение><новое значение> @Agent_name```, чтобы обратиться к обработчику команд и изменить в памяти определённого агента информацию, которая расходиться с той что находиться в его памяти. Таким образом, агент CLI_Agent может поддерживать точность и актуальность информации в групповом чате.

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/02d4e87b-cd06-475e-a3b4-46668c328320)

Если команда `!edit_memory` является заготовленной, то `&command_name` - это пользовательские команды. Например в конвейере Conveyer2Autogen есть отдел создания списка ролей. Мы тестировали этот отдел и нам всегда приходилось корректировать его работу писав на фидбэках: "выведи список полностью", "эта роль является лишней", "в списке слишком мало ролей - добавь ещё". Эту проблему решил Regulatory agent класса CLI_Agent использовавший пользовательские команды:
* `&cleared_out` - обозначает, что список написан неправильно и не касается задачи пользователя
* `&add_more` - обозначает, что список слишком короткий и его нужно увеличить
* `&only_list` - обозначает, что агент вывел что-то ещё кроме списка, что следовало исправить

Пользователь точно также может описать агенту класса CLI_Agent свои условия срабатывания и точно также получать верные результаты!

## Два варианта реализаци класса CLI_Agent
<a name="ClI_Agent_2"></a> 
При использовании autogen очевидно, что правильным подходом является "один агент - одна функция", а поэтому есть два варианта реализации класса CLI_Agent.

**1. Обработчик находиться изначально в AssistantAgent:**

![9](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/af71986e-b2ce-4c0f-a3af-0566d2030ffe)

**2. Обработчик является отдельным классом агентов:**

![11](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/644bc01b-d94e-400f-8645-d9876cff31e3)

# универсальный агент "Мотиватор"
Исходя из нашего опыта использования autogen агенты бывают двух типов:

![AgentTypesRu](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/c1a509a0-1b17-42c9-87bd-ce2a809ab122)

Всё чаще и чаще стали появляться исследовании о влиянии определенных манипуляций/мотиваций в промтах на качество ответа LLM. Мы решили собрать все промты из этих исследований (начиная с "От этого зависит моя карьера..." до "Сейчас май...") и создать агента, что будет подстраиваться под каждую роль в чате и перед тем как она что-то напишет улучшать её результат с помощью подобного "Франкенштейна". Мы назвали этого агента "Мотиватор" и используем его по сей день в autogen. Но этот агент точно требует доработки так как мы все понимаем, что даже одного слово в промте может испортить результат, а поэтому нужно досканально проанализировать комбинации промтов которые мы брали из иследований, а также их полезность, так как например шаблон "...я заплачу тебе 200$ за то что ты сделаешь это правильно" пришлось убрать так как он лишь ухудшает результат и автор этого шаблона ошибся в своей работе о чем писал в соц.сетях.
# Conveyer2Autogen
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/6b8acf93-9bf4-42d0-97ed-051dd25d541a)

Conveyer2Autogen - позволяет пользователю получить целый групповой чат autogen с правильно прописанными и протестированными промтами, интегрированными утилитами в агентов, а также function call. Также Conveyer2Autogen из-за своей простоты подхода "prompt --> answer и ничего лишнего" имеет свой TUI. Мы считаем это очень важной деталью так как это позволит большему количеству людей познакомиться с autogen и начать им пользоваться без написания кода 

## **Conveyer2Autogen pipeline**

<details>
<summary> <i>  нажмите сюда для просмотра </i> </summary>
  
![ALL](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/2e1524cf-6a9d-4030-843f-f07a65990e1f)

</details>

* Этот pipeline не точен так как должен быть больше как минимум в два раза так как мы должны использовать функциональный подход: "один агент - одна функция".

### разбор пайплана Conveyer2Autogen
```
Глобальный план работы Conveyer2Autogen:
1. Создание списка ролей/агентов по промту пользователя
2. Выбор класса для каждого агента в списке
3. Написание промта для каждого агента в списке
4. Анализ того нужен ли кому нибудь в списке агентов интегрируемый инструмент
  4.1. Интеграция инструмента для определенного агента
5. Анализ того нужен ли кому нибудь в списке агентов function call
  5.1 Поиск кода function call в rag и его интеграция в агента
  5.2 Если код function call не найден - генерация кода function call и его интеграция в агента
6. Сборка
```
### 1. Создание списка ролей/агентов
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/0b533c40-1b21-4ca5-9981-5493ba17add4)

Здесь всё интуитивно понятно на входе промт пользователя, а уже на выходе список ролей. Здесь как раз таки видно как примерно будут встраиваться Regulatory Agent (класса CLI_Agent) и Motivator в групповые чаты autogen.

* **Пример реализации в TUI:**
  
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/6d83f249-7e80-4597-8788-a2cee91573d7) ![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/9590f64e-1054-42cb-964b-ad41683be8b1)

### 2. Выбор класса для каждого агента в списке
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/cd8f60a1-145a-4def-979a-ba6fc8d8659f)

Далее сгенерированный список названий агентов поступает в отдел подбора класса и хоть может показаться, что это достаточно простой процесс, ведь почти все классы будут одинаковые - на самом деле это не так. Также мы рассматриваем добавление RetrieveUserProxyAgent и RetrieveAssistantAgent в список подбора с возможностью автоматического заполнения RAG с помощью LLM_web_search, SerpAPI или других подобных инструментов.
* **Пример реализации в TUI:**

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/746c212c-925c-4744-b58e-462a2c6052c8)

### 2.1 Сборка конфигурации autogen
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/206eb49a-e71e-4c2a-ad06-de1193d4d57f)

Этот пункт должен находиться в самом начале вместе с вводом промта от пользователя, но из-за технических неполадок он здесь (очевидно, что без доступа к llm пункт 1 и 2 невозможен..). В этом пункте мы не используем групповой чат autogen, а лишь запрашиваем API ключ и вставляем его в llm_config. Важно заметить, что API ключ не обязательно должен быть от ChatGPT, он также может быть и от textgen-webui, а поэтому пользователь может использовать и open-source модели. Также уже разработан вариант сборки мультимодельных конфигураций.

### 3. Генерация role-prompt для каждого агента
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/d722cf75-9b04-4789-8a32-a883d57bd22e)
```
Как вы видете в этот отдел данные поступают уже в формате:
  ________________________________________ 
 | 1. AgentName | llm_config | Class name |
 | N. AgentName | llm_config | Class name |
 |________________________________________|
```

**Очень важные нюансы, что не удалось отобразить в pipeline:**
1. Prompt engineer пользуется определенными правилами для написания промтов. Вот часть этих правил:
<details>
<summary> <i>  нажмите сюда для просмотра </i> </summary>
  
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/9aecabf4-d0d4-44d6-adb2-6da2b8c0e826)

</details>

2. Prompt engineer заполняет определенные универсальные шаблоны. Например:

```
Обозначание личности: <вставь сюда имя агента>, "ты находишь в групповом чате по" <опиши здесь задачу группового чата в котором находиться агент>
...и т.д.
```
  Такие шаблоны могут быть просто огромными их мы обычно называем: промт-структуры и они достаточно сильно повышают точность так как ограничывают ответ LLM в строгих рамках.

3. Мы хотим проводить комплексный анализ промта с помощью [promptfoo](https://github.com/promptfoo/promptfoo), [prompt-testing-framework](https://github.com/RafalWilinski/prompt-testing-framework), [prompttools](https://github.com/hegelai/prompttools), [promptimize](https://github.com/preset-io/promptimize) для огромного скачка точности.

### 4. Добавление инстурмента
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/4c7fbf7a-6c77-47e4-8684-d105907221ea)
```
Как вы видете в этот отдел данные поступают уже в формате:
  _________________________________________________
 | 1. AgentName | llm_config | Class name | prompt |
 | N. AgentName | llm_config | Class name | prompt |
 |_________________________________________________|
```
**Очень важные нюансы, что не удалось отобразить в pipeline:**
1. Если нужный инструмент не был найден в RAG - пользователь может дать нам его код в txt файле и мы правильно интегрируем его
2. Поиск и интеграция инструмента производится не для каждого агента - мы не должны перебарщивать с ними

### 5. Добавление function call

**Данный отдел состоит из нескольких подотделов:**
1. анализ нужен ли агенту function call
2. поиск function call и его добавление
3. написание function call и его добавление
```
Данные в этот подотдел поступают в формате:
  ___________________________________________________________________
 | 1. AgentName | llm_config | Class name | prompt | tool (optional) |
 | N. AgentName | llm_config | Class name | prompt | tool (optional) |
 |___________________________________________________________________|
```
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/93d4b6c3-1cf9-44ca-be4e-f3f99b3f3be1)

Всё показано достаточно примитивно и без половины вспомогательных агентов так как мы считаем, что этого достаточно для простого ознакомления.

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/f2f18738-2c5e-48d8-a85c-88619fd48f34)

Как раз-таки здесь мы видим что подотделы можно использовать при срабатывании определенных условий. Любой отдел не готов к любым данным, но если типов данных всего два, то почему бы не сделать два подотдела для работы с каждым.

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/640e09a1-8f43-4182-844c-e3e1a7dbc7cb)

### 6. Сборка
```
это финальный этап любого конвейера, а именно в этот данные поступают в формате:
  ______________________________________________________________________________________________
 | 1. AgentName | llm_config | Class name | prompt | tool (optional) | function call (optional) |
 | N. AgentName | llm_config | Class name | prompt | tool (optional) | function call (optional) |
 |______________________________________________________________________________________________|
```
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/8cf367e9-c765-46ce-a0aa-643f5dc9e3e2)
Именно здесь мы запускаем чат для пользователя где он может получить свой идеальный ответ.

## Ссылочная модель общения агентов

Нам очень сильно мешал тот факт, что лишь один агент пишет одному (при чем соблюдая опредленную очередь) - это очень сильно замедляет процесс работы группового чата поэтому в более больших отделах мы добавили "ссылочную модель" каждому агенту. Что такое ссылочная модель? Грубо говоря это абстракция для агента о том как обращаться в одном сообщении сразу к нескольким людям используя @ чтобы отмечать каждого по нику. Лишь это маленькое дополнение в разы увеличило производительность и эффективность чата. Сюда конечно можно прикрепить огромное количество скриншотов, но все работает примерно по одному шаблону:
```
Role_Name (to chat_manager):

@Role_name_2 ....text...
@Role_name_3 ....text...
@Role_name_4 ....text...
```
Это хорошая альтернатива орекстратору, но для её работаспособности нужно откатить pyautogen до версии 0.1.14 

# Conveyer2Conveyer
1. описание
# Autogen-Ship
1. Описание
2. Три варианта создания отдела:
  2.1 Conveyer2Autogen
  2.2 Conveyer2Conveyer
  2.3 Вручную
