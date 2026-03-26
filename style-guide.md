.sh文件命名规范 snake_case, backup_database.sh
全局变量与环境变量 规范： 全部大写，单词间用下划线。如 LOG_DIR，API_KEY 范围： 用于环境变量或脚本范围内跨函数使用的配置。
局部变量，全部小写，单词间用下划线。强制要求： 在函数内部必须使用 local 关键字声明，否则它会污染全局作用域。local file_path

常量 (Readonly) 规范： 全部大写。定义： 使用 readonly 关键字明确标示。如 readonly VERSION="1.2.0"，readonly LOG_DIR

函数命名规范，规范： 全部小写，单词间用下划线。建议以动词开头。现代规范建议省略 function 关键字，直接使用 name()。

私有函数前缀： 如果你的脚本很大，内部使用的私有函数可以加下划线前缀，如 __internal_helper()。

引用变量： 虽然不是命名规范，但始终给变量加双引号（如 "$VAR"）是 Linux 脚本编写中最核心的安全规范。
文件夹用 - 而不是 _
