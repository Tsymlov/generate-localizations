Ниже — Swift-скрипт, который:

✅ Делает следующее:
Читает translations.csv — где строки локализации по ключам и языкам.
Создаёт:
Localizable.strings для iOS (.lproj)
strings.xml для Android (res/values-<lang>)
🗂 Ожидаемый формат translations.csv:

key,en,ru
app_name,My App,Моё Приложение
welcome_text,Welcome!,Добро пожаловать!
login_button,Login,Войти
🧾 Swift-скрипт: generate_localizations.swift

>import Foundation
>
>// MARK: - Config
>
>let csvPath = "translations.csv"
>let outputPath = "LocalizationOutput" // Создаст папку с результатом
>
>// MARK: - Helper Functions
>
>func parseCSV(filePath: String) -> ([String], [[String: String]]) {
>    guard let content = try? String(contentsOfFile: filePath) else {
>        fatalError("Не удалось прочитать CSV по пути: \(filePath)")
>    }
>
>    let lines = content.components(separatedBy: .newlines).filter { !$0.isEmpty }
>    let headers = lines[0].components(separatedBy: ",")
>
>    var rows: [[String: String]] = []
>
>    for line in lines.dropFirst() {
>        let values = line.components(separatedBy: ",")
>        var row = [String: String]()
>
>        for (index, header) in headers.enumerated() where index < values.count {
>            row[header] = values[index]
>        }
>        rows.append(row)
>    }
>
>    return (headers, rows)
>}

func writeIOSStrings(for lang: String, rows: [[String: String]]) {
    let folder = "\(outputPath)/iOS/\(lang).lproj"
    try? FileManager.default.createDirectory(atPath: folder, withIntermediateDirectories: true)

    var output = ""
    for row in rows {
        if let key = row["key"], let value = row[lang] {
            output += "\"\(key)\" = \"\(value)\";\n"
        }
    }

    let filePath = "\(folder)/Localizable.strings"
    try? output.write(toFile: filePath, atomically: true, encoding: .utf8)
}

func writeAndroidStrings(for lang: String, rows: [[String: String]]) {
    let folder = lang == "en"
        ? "\(outputPath)/Android/values"
        : "\(outputPath)/Android/values-\(lang)"
    try? FileManager.default.createDirectory(atPath: folder, withIntermediateDirectories: true)

    var output = "<resources>\n"
    for row in rows {
        if let key = row["key"], let value = row[lang] {
            let escaped = value.replacingOccurrences(of: "'", with: "\\'")
            output += "    <string name=\"\(key)\">\(escaped)</string>\n"
        }
    }
    output += "</resources>"

    let filePath = "\(folder)/strings.xml"
    try? output.write(toFile: filePath, atomically: true, encoding: .utf8)
}

// MARK: - Main

let (headers, rows) = parseCSV(filePath: csvPath)
let languages = headers.dropFirst() // убираем "key"

for lang in languages {
    writeIOSStrings(for: lang, rows: rows)
    writeAndroidStrings(for: lang, rows: rows)
}

print("✅ Локализации успешно сгенерированы в папке '\(outputPath)'")
🚀 Как использовать

Сохрани translations.csv в ту же папку, что и скрипт.
Запусти:
swift generate_localizations.swift
Получишь структуру:
LocalizationOutput/
├── Android/
│   ├── values/strings.xml       ← en
│   └── values-ru/strings.xml    ← ru
└── iOS/
    ├── en.lproj/Localizable.strings
    └── ru.lproj/Localizable.strings

