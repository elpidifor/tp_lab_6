# tp_lab_6

Изучение фреймворков для тестирования на примере Catch

## Tasks

- [ ] 1. Ознакомиться со ссылками учебного материала
- [ ] 2. Выполнить инструкцию учебного материала
- [ ] 3. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```bash
$ export GITHUB_USERNAME=<имя_пользователя> // Создание переменной с именем пользователя на GitHub
$ export GIST_TOKEN=<сохраненный_токен> // Создание перменной, которая хранит токен
$ alias edit=vim // Открытие текстового редактора
```

```ShellSession
$ mkdir -p ${GITHUB_USERNAME}/workspace // Создание директория workspace
$ cd ${GITHUB_USERNAME}/workspace // Переход в нужную директорию
$ git remote remove origin // Удаление старого origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06 // Установка нового origin-репозитория
```

```ShellSession
$ mkdir tests
$ wget https://github.com/philsquared/Catch/releases/download/v1.9.3/catch.hpp -O tests/catch.hpp // wget загружает пакеты из Сети
$ cat > tests/main.cpp <<EOF
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
EOF
```

```ShellSession
$ gsed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\ // Редактирование файлов локально (с резервной копией)
option(BUILD_TESTS "Build tests" OFF)
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
$ cat >> CMakeLists.txt <<EOF

if(BUILD_TESTS)
enable_testing()
file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES})
target_link_libraries(check \${PROJECT_NAME} \${DEPENDS_LIBRARIES})
add_test(NAME check COMMAND check "-s" "-r" "compact" "--use-colour" "yes")
endif()
EOF
```

```ShellSession
$ cat >> tests/test1.cpp <<EOF // Запись в файл
#include "catch.hpp"
#include <print.hpp>

TEST_CASE("output values should match input values", "[file]") {
std::string text = "hello";
std::ofstream out("file.txt");

print(text, out);
out.close();

std::string result;
std::ifstream in("file.txt");
in >> result;

REQUIRE(result == text);
}
EOF
```

```ShellSession
$ cmake -H. -B_build -DBUILD_TESTS=ON // Эффективное отображание текущих настро CMake, которые затем могут быть изменены с опцией -D.
/*-- The C compiler identification is AppleClang 9.0.0.9000039
-- The CXX compiler identification is AppleClang 9.0.0.9000039
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/elpidifor/elpidifor/workspace/projects/lab06/_build*/
$ cmake --build _build // Компиляция
/*Scanning dependencies of target print
[ 20%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 40%] Linking CXX static library libprint.a
[ 40%] Built target print
Scanning dependencies of target check
[ 60%] Building CXX object CMakeFiles/check.dir/tests/main.cpp.o
[ 80%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Linking CXX executable check
[100%] Built target check*/
$ cmake --build _build --target test // Запуск тестов
/*Running tests...
Test project /Users/elpidifor/elpidifor/workspace/projects/lab06/_build
Start 1: check
1/1 Test #1: check ............................   Passed    0.02 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.03 sec*/
```

```ShellSession
$ _build/check -s -r compact // Проверка сборки
/*//Users/elpidifor/elpidifor/workspace/projects/lab06/tests/test1.cpp:15: PASSED: result == text for: "hello" == "hello"
Passed 1 test case with 1 assertion.*/
$ cmake --build _build --target test -- ARGS=--verbose
/*Running tests...
UpdateCTestConfiguration  from :/Users/elpidifor/elpidifor/workspace/projects/lab06/_build/DartConfiguration.tcl
UpdateCTestConfiguration  from :/Users/elpidifor/elpidifor/workspace/projects/lab06/_build/DartConfiguration.tcl
Test project /Users/elpidifor/elpidifor/workspace/projects/lab06/_build
Constructing a list of tests
Done constructing a list of tests
Updating test list for fixtures
Added 0 tests to meet fixture requirements
Checking test dependency graph...
Checking test dependency graph end
test 1
Start 1: check

1: Test command: /Users/elpidifor/elpidifor/workspace/projects/lab06/_build/check "-s" "-r" "compact" "--use-colour" "yes"
1: Test timeout computed to be: 10000000
1: /Users/elpidifor/elpidifor/workspace/projects/lab06/tests/test1.cpp:15: PASSED: result == text for: "hello" == "hello"
1: Passed 1 test case with 1 assertion.
1:
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.02 sec*/
```

```ShellSession
$ gsed -i 's/lab05/lab06/g' README.md // Редактирование файлов
$ gsed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml // редактирование файлов
$ gsed -i '/cmake --build _build --target install/a\
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

```ShellSession
$ travis lint // Проверка файла .travis.yml
/*Warnings for .travis.yml:
[x] value for addons section is empty, dropping
[x] in addons section: unexpected key apt, dropping*/
```

```ShellSession
$ git add . // Добавление файла
$ git commit -m"added tests" // Комментарий к коммиту
$ git push origin master // отправка
/*Counting objects: 43, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (35/35), done.
Writing objects: 100% (43/43), 82.49 KiB | 2.84 MiB/s, done.
Total 43 (delta 9), reused 0 (delta 0)
remote: Resolving deltas: 100% (9/9), done.
To https://github.com/elpidifor/lab06
* [new branch]      master -> master*/
```

```ShellSession
$ travis login --auto // Автонастройка Travis
$ travis enable // Аткивация
/*Detected repository as elpidifor/lab06, is this correct? |yes|
repository not known to Travis CI (or no access?)
triggering sync: . done
elpidifor/lab06: enabled :)*/
```

```ShellSession
$ mkdir artifacts // Создание папки artifacts
$ sleep 20s && screencapture artifacts/screenshot.png // Переход в спящий режим на 20с, затем скриншот экрана
# for macOS: $ screencapture -T 20 artifacts/screenshot.png
# open https://github.com/${GITHUB_USERNAME}/lab06
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=06
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Google Test](https://github.com/google/googletest)

```
Copyright (c) 2017 Братья Вершинины
```
