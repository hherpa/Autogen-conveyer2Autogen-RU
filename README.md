# Требуемые нововведения в autogen
## Autogen-conveyer и его отделы:
1. Что это и зачем это нужно? (* Autogen-conveyer - это новый подход к....)
2. из чего состоит конвейер (отделы, подотделы)
3. основная проблема: парсинг результата отдела (+ наша идея с агентов что будет помечать результат чата тегом @final)
4. pipeline
## ClI_Agent
1. что это и зачем это нужно
2. варианты реализации (обработчик отдельно и обработчик в самом агенте)
3. pipeline1 & pipeline2
![9](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/af71986e-b2ce-4c0f-a3af-0566d2030ffe)
![10](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/e641d7ba-843d-46f6-8782-eb93ef2342d4)

# Conveyer2Autogen
Conveyer2Autogen - позволяет пользователю получить целый групповой чат autogen с правильно прописанными и протестированными промтами, интегрированными утилитами в агентов, а также function call. Также Conveyer2Autogen из-за своей простоты подхода "prompt --> answer и ничего лишнего" имеет свой TUI.

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/fe76a21f-42df-4efa-8ec9-47c9e0c36346)





1. разбор Pipeline по частям
2. Этот pipeline не точен так как должен быть больше как минимум в два раза....правило дифференцирования: один агент одна функция
3. Ссылочная модель за место орекстратора
# Conveyer2Conveyer
1. описание
# Autogen-Ship
1. Описание
2. Три варианта создания отдела:
  2.1 Conveyer2Autogen
  2.2 Conveyer2Conveyer
  2.3 Вручную
