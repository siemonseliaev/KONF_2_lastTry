import os
from pathlib import Path
import sys
import tomllib

CONFIG_FILE = Path(__file__).parent / "config.toml"


def load_config(file_path: Path) -> dict:
    """Загружает и парсит TOML файл конфигурации."""
    if not file_path.exists():
        raise FileNotFoundError(
            f"Конфигурационный файл '{file_path}' не найден."
        )

    try:
        with open(file_path, "rb") as f:
            return tomllib.load(f)
    except tomllib.TOMLDecodeError as e:
        raise ValueError(
            f"Ошибка синтаксиса в TOML файле: {e}"
        )


def validate_config(config: dict) -> None:
    """Проверяет наличие и типы всех параметров."""
    required_fields = {
        "package_name": str,
        "repo_url": str,
        "repo_mode": str,
        "package_version": str,
        "output_image": str,
        "max_depth": int,
    }

    for field, exp_type in required_fields.items():
        if field not in config:
            raise KeyError(
                f"Отсутствует обязательный параметр: '{field}'"
            )

        value = config[field]
        is_bool = isinstance(value, bool)
        if not isinstance(value, exp_type) or (
            exp_type is int and is_bool
        ):
            raise TypeError(
                f"Неверный тип для параметра '{field}'. "
                f"Ожидался {exp_type.__name__}, "
                f"получен {type(value).__name__}"
            )

    if config["max_depth"] < 0:
        raise ValueError(
            "Параметр 'max_depth' не может быть отрицательным."
        )

    valid_modes = ["online", "offline", "test"]
    if config["repo_mode"] not in valid_modes:
        raise ValueError(
            f"Некорректный режим 'repo_mode' "
            f"('{config['repo_mode']}'). "
            f"Допустимые значения: {valid_modes}"
        )


def main():
    print("=== Инициализация CLI-приложения ===")
    try:
        config = load_config(CONFIG_FILE)
        validate_config(config)

        print("\n[Успешно] Конфигурация загружена:")
        for key, value in config.items():
            print(f"  - {key} = {value}")

    except (
        FileNotFoundError,
        ValueError,
        KeyError,
        TypeError,
    ) as e:
        print(
            f"\n[ОШИБКА КОНФИГУРАЦИИ]: {e}",
            file=sys.stderr,
        )
        sys.exit(1)


if __name__ == "__main__":
    main()
