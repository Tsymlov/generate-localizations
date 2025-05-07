# Generate Localizations
Swift-скрипт, который ✅ делает следующее:
- Читает translations.csv — где строки локализации по ключам и языкам.
- Создаёт:
    * Localizable.strings для iOS (.lproj)
    * strings.xml для Android (res/values-<lang>)

🗂 Ожидаемый формат translations.csv:

> 
>     key,en,ru
>     app_name,My App,Моё Приложение
>     welcome_text,Welcome!,Добро пожаловать!
>     login_button,Login,Войти
> 

## 🚀 Как использовать 

1. Сохрани translations.csv в ту же папку, что и скрипт.
2. Запусти:

>     bash
> 
>     swift generate_localizations.swift
> 

3. Получишь структуру:

> 
>      LocalizationOutput/
>      ├── Android/
>      │   ├── values/strings.xml       ← en
>      │   └── values-ru/strings.xml    ← ru
>      └── iOS/
>          ├── en.lproj/Localizable.strings
>          └── ru.lproj/Localizable.strings
> 

