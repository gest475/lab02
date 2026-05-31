# Лабораторная работа №2 — Git

**Студент:** Артеменко Арина ИУ8-22
**Репозиторий:** https://github.com/gest475/lab02

## Цель
Изучить систему контроля версий Git: init, commit, branch, pull request, rebase, merge.

## Шаги выполнения

### Часть I. Базовый workflow

```bash
git init
git config user.name "gest475"
git config user.email "arina.lisichkina@bk.ru"
git remote add origin https://github.com/gest475/lab02.git
git add README.md
git commit -m "Initial commit"
git push -u origin main

# Добавление hello_world.cpp (плохой стиль)
cat > hello_world.cpp <<EOF
#include <iostream>
using namespace std;

int main() {
    cout << "Hello world" << endl;
    return 0;
}
EOF

git add hello_world.cpp
git commit -m "Add hello_world.cpp with bad style"
git push

# Добавление ввода имени пользователя
cat > hello_world.cpp <<EOF
#include <iostream>
#include <string>
using namespace std;

int main() {
    string name;
    cout << "Enter your name: ";
    cin >> name;
    cout << "Hello world from " << name << endl;
    return 0;
}
EOF

git commit -am "Add user input and greeting"
git push

## часть 2 Ветка patch1 и Pull Request
git checkout -b patch1

# Исправление стиля (убираем using namespace std)
cat > hello_world.cpp <<EOF
#include <iostream>
#include <string>

int main() {
    std::string name;
    std::cout << "Enter your name: ";
    std::cin >> name;
    std::cout << "Hello world from " << name << std::endl;
    return 0;
}
EOF

git commit -am "Fix code style: remove using namespace std"
git push --set-upstream origin patch1

# Создание Pull Request через GitHub
# PR вмержен в main, ветка patch1 удалена

git checkout main
git pull
git branch -d patch1

## часть 3 Ветка patch2 + rebase
git checkout -b patch2

# Применение стиля Mozilla
cat > hello_world.cpp <<EOF
#include <iostream>
#include <string>

int
main()
{
    std::string name;
    std::cout << "Enter your name: ";
    std::cin >> name;
    std::cout << "Hello world from " << name << std::endl;
    return 0;
}
EOF

git commit -am "Apply Mozilla code style"
git push --set-upstream origin patch2

# Создание конфликта (изменение main на GitHub)
# Добавлен комментарий // Comment from main branch

# Разрешение конфликта через rebase
git fetch origin
git rebase origin/main

# После конфликта — исправление файла
cat > hello_world.cpp <<EOF
// Comment from main branch
#include <iostream>
#include <string>

int
main()
{
    std::string name;
    std::cout << "Enter your name: ";
    std::cin >> name;
    std::cout << "Hello world from " << name << std::endl;
    return 0;
}
EOF

git add hello_world.cpp
git rebase --continue
git push --force-with-lease

# PR вмержен, ветка patch2 удалена
git checkout main
git pull
git branch -d patch2

## сборка и запуск
g++ -std=c++11 hello_world.cpp -o hello_world
./hello_world


## Ответы на вопросы 

**1. Чем отличается merge от rebase?**

Merge — это когда я беру и соединяю две ветки, появляется отдельный коммит слияния. Rebase — это когда мои коммиты как бы "переезжают" на другую ветку, история становится ровной, без ответвлений. Я использую rebase, чтобы было красиво и понятно, но после него приходится делать push -f, потому что коммиты изменились.

**2. Что такое Pull Request и зачем он нужен?**

Pull request (или PR) — это запрос на слияние моей ветки с основной на GitHub. Я делаю изменения, создаю PR, а другие люди смотрят мой код, пишут замечания, и если всё хорошо — разрешают влить. Это нужно, чтобы кто-то проверил код перед тем, как он попадёт в общий репозиторий.

**3. Как разрешать конфликты?**

Конфликт появляется, когда два человека меняют одни и те же строки в файле. Git пишет в файле специальные скобки: <<<<<<<, ======= и >>>>>>>. Я открываю файл, смотрю, что к чему, оставляю нужный код, удаляю эти скобки, сохраняю, потом делаю git add и git rebase --continue. Всё, конфликт решён.

**4. Зачем нужен git push -f и почему это опасно?**

Push -f (force push) нужен, чтобы перезаписать удалённую ветку. Обычно я его использую после rebase, потому что обычный push не работает — коммиты изменились. Опасно это тем, что можно стереть чужие изменения, если кто-то тоже работал в этой ветке. Поэтому лучше использовать git push --force-with-lease — он проверяет, нет ли новых чужих коммитов, и только потом перезаписывает. В main ветку force push вообще нельзя делать.
