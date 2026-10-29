«Инфраструктурный репозиторий»

Блок 1. Основы
Задание 1.1. Настройка и первый коммит

Команды:

bash
git config --global user.name "Айдана Маликова"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git clone git@github.com:Akushkadana/infra-project.git
cd infra-project
printf '.env\n*.log\n*.retry\n' > .gitignore
git add .gitignore
git commit -m "chore: add .gitignore"
git push -u origin main

Содержимое .gitignore:

.env
*.log
*.retry

Что произошло: я настроила Git, создала пустой репозиторий infra-project на GitHub и склонировала его по SSH. Затем создала .gitignore с тремя строками (.env, *.log, *.retry) и сделала из него первый коммит: в нём лежит только этот файл.

Задание 1.2. Файлы проекта

Команды:

bash
cp -r ~/midterm/infra-project-starter/. .
echo "DB_PASSWORD=secret123" > .env
echo "test log" > app.log
git status
git add README.md .env.example
git commit -m "docs: add README and .env.example"
git add docker/
git commit -m "feat(docker): add Dockerfile and index page"
git add ansible/
git commit -m "feat(ansible): add inventory and playbook"
git push

Файлы в репозитории на GitHub

Я скопировала файлы из архива в репозиторий и создала файлы .env и app.log. Git их не показывает, потому что их имена перечислены в .gitignore. После этого я сделала три отдельных коммита (README и .env.example, docker/, ansible/) и отправила их на GitHub командой git push.

Задание 1.3. Просмотр изменений

Команды:

bash
echo "Учебный проект: Docker-образ и Ansible-плейбук для веб-сервера." >> README.md
git diff
git add README.md
git diff
git diff --staged
git commit -m "docs: describe project in README"

Почему второй git diff пустой: git diff сравнивает рабочую папку с индексом (то, что я изменила, но ещё не добавила). После git add README.md изменение попало в индекс, и рабочая папка стала такой же, как индекс, поэтому различий нет. А git diff --staged сравнивает индекс с последним коммитом, поэтому он снова показывает добавленную строку.

Блок 2. История и ветки
Задание 2.1. История коммитов

Команды:

bash
git log --oneline
git log --oneline --graph --all
git show HEAD~1

Вывод git log --oneline:

1b0de6e docs: add README and .env.example
51c9e9e docs: describe project in README
42d9d13 feat(ansible): add inventory and playbook
9677c68 feat(docker): add Dockerfile and index page
35d9283 docs: add README and .env.example
0e7b8e2 chore: add .gitignore

Вывод git log --oneline --graph --all:

* 1b0de6e (HEAD -> main, origin/main) docs: add README and .env.example
* 51c9e9e docs: describe project in README
* 42d9d13 feat(ansible): add inventory and playbook
* 9677c68 feat(docker): add Dockerfile and index page
* 35d9283 docs: add README and .env.example
* 0e7b8e2 chore: add .gitignore

Вывод git show HEAD~1 (первые строки):

commit 51c9e9e10bcf65674bdb94f1222b8c0a5a200987
Date:   Thu Oct 29 14:07:32 2026 +0600

    docs: describe project in README

diff --git a/README.md b/README.md
index 27ca4e3..e5b42ea 100644
--- a/README.md
+++ b/README.md
@@ -3,3 +3,4 @@
 <!-- Допишите сюда -->
+Docker и Ansible

HEAD~1 — это коммит: 51c9e9e — «docs: describe project in README» (вторая строка сверху в git log --oneline, то есть коммит перед последним).

Задание 2.2. Ветка и слияние

Команды:

bash
git switch -c feature/healthcheck
echo 'HEALTHCHECK CMD wget -qO- http://localhost/ || exit 1' >> docker/Dockerfile
git commit -am "feat(docker): add healthcheck"
git switch main
git merge feature/healthcheck
git branch -d feature/healthcheck
git push

Что произошло: я создала ветку feature/healthcheck, добавила в конец docker/Dockerfile строку HEALTHCHECK и закоммитила. Затем вернулась в main и слила ветку. Получился Fast-forward, потому что в main за это время не появилось новых коммитов, и Git просто передвинул указатель вперёд. После слияния я удалила ветку командой git branch -d.

Задание 2.3. Конфликт слияния

Команды:

bash
git switch -c fix/app-port
# в ansible/group_vars/all.yml app_port изменён на 9090
git commit -am "fix(ansible): set app_port to 9090"
git switch main
# в ansible/group_vars/all.yml app_port изменён на 8000
git commit -am "fix(ansible): set app_port to 8000"
git merge fix/app-port
# файл отредактирован, маркеры удалены
git add ansible/group_vars/all.yml
git commit --no-edit
git push

Содержимое ansible/group_vars/all.yml С маркерами конфликта:

---
<<<<<<< HEAD
app_port: 8000
=======
app_port: 9090
>>>>>>> fix/app-port
app_user: deploy

Итоговое содержимое ansible/group_vars/all.yml (маркеров нет):

---
app_port: 8000
app_user: deploy

Что произошло: я изменила одну и ту же строку app_port в двух ветках по-разному (9090 в fix/app-port и 8000 в main), поэтому при git merge Git сообщил о конфликте. Я открыла файл, оставила значение 8000, удалила три строки с маркерами <<<<<<<, =======, >>>>>>>, выполнила git add и git commit. Получился merge-коммит.

Блок 3. Удалённый репозиторий и Merge Request
Задание 3.1. Получение изменений с сервера

Что сделано: я отредактировала README.md через веб-интерфейс GitHub (кнопка Edit) и сохранила изменение коммитом в main.

Коммит из веб-интерфейса

Команды:

bash
git pull
git log --oneline

Что произошло: git pull скачал с GitHub коммит, сделанный в веб-интерфейсе, и добавил его в мою локальную ветку main. Теперь он виден в git log.

Задание 3.2. Merge Request

Команды:

bash
git switch -c feature/ansible-curl
# в ansible/playbook.yml добавлена задача «Install curl»
git commit -am "feat(ansible): install curl"
git push -u origin feature/ansible-curl
# Pull Request создан и слит через веб-интерфейс
git switch main
git pull

Ссылка на слитый Pull Request: ВСТАВЬТЕ ССЫЛКУ

Описание Pull Request: «Добавлена задача установки curl в ansible/playbook.yml. Это нужно, чтобы на серверах была утилита для проверки HTTP-запросов и автоматизации.»

Добавленная задача в ansible/playbook.yml:

yaml
    - name: Install curl
      ansible.builtin.package:
        name: curl
        state: present

Сообщение коммита: feat(ansible): install curl (формат Conventional Commits: тип, область, описание).

Скриншот: страница Pull Request со статусом Merged и описанием.

Слитый PR feature/ansible-curl

Что произошло: я создала ветку, добавила задачу установки curl в плейбук и отправила ветку на GitHub. Затем открыла Pull Request в main с описанием, слила его в веб-интерфейсе и подтянула результат локально командами git switch main и git pull.

Задание 3.3. Уборка веток

Команды:

bash
git branch -d feature/ansible-curl
git push origin --delete feature/ansible-curl
git fetch --prune
git branch -a

Что произошло: я удалила слитую ветку локально и на сервере. Команда git fetch --prune убрала устаревшие ссылки на удалённые ветки, и в списке остались только main и remotes/origin/main.

Блок 4. Docker

Команды:

bash
git switch main
git pull
git switch -c feature/docker-page
# изменены docker/index.html (своя страница), docker/Dockerfile (EXPOSE 80), README.md (раздел «Запуск в Docker»)
docker build -t infra-web:1.0 docker/
docker run -d --name infra-web -p 8080:80 infra-web:1.0
curl http://localhost:8080
docker rm -f infra-web
git add docker/Dockerfile docker/index.html README.md
git commit -m "feat(docker): add personal page and expose port"
git push -u origin feature/docker-page
# Pull Request создан и слит через веб-интерфейс

Итоговый docker/Dockerfile:

FROM nginx:1.27-alpine
COPY index.html /usr/share/nginx/html/
EXPOSE 80
HEALTHCHECK CMD wget -qO- http://localhost/ || exit 1

Раздел «Запуск в Docker» в README.md:

bash
docker build -t infra-web:1.0 docker/
docker run -d --name infra-web -p 8080:80 infra-web:1.0
curl http://localhost:8080
docker rm -f infra-web

Слитый PR feature/docker-page

Страница на localhost:8080

Что произошло: я заменила docker/index.html на страницу со своим именем и названием проекта, добавила EXPOSE 80 после строки COPY, собрала образ infra-web:1.0 и запустила контейнер на порту 8080. curl показал мою страницу. Затем я удалила контейнер, добавила в README раздел «Запуск в Docker», закоммитила в формате Conventional Commits и слила изменения в main через Pull Request.

Блок 5. Исправление ошибок
Задание 5.1. Отмена изменений и правка коммита

Команды:

bash
echo "# лишняя строка" >> docker/Dockerfile
git status
git restore docker/Dockerfile
git status
echo "Автор: Айдана Маликова" >> README.md
git add README.md
git commit -m "docs: updte readme"
git commit --amend -m "docs: update readme"
git push

Что произошло: я дописала лишнюю строку в docker/Dockerfile и вернула файл в исходное состояние командой git restore. Затем сделала коммит с опечаткой updte и исправила сообщение командой git commit --amend до git push, поэтому на сервере опечатки нет.

Задание 5.2. Отмена опубликованного коммита

Команды:

bash
sed -i '/^\*\.log$/d' .gitignore
git commit -am "chore: remove *.log from .gitignore"
git push
git log --oneline -1
git revert ХЭШ_КОММИТА
git push

Содержимое .gitignore после revert (строка *.log на месте):

.env
*.log
*.retry

Почему используют revert, а не удаляют коммит из истории: коммит, который уже отправлен на сервер, могли скачать другие люди. Если удалить его из истории, придётся переписывать историю и делать force-push, и у других участников история разойдётся с серверной. git revert ничего не стирает, а добавляет новый коммит, который отменяет изменение, поэтому это безопасно и всё видно в истории.

Задание 5.3. Контрольные вопросы

1. Что такое коммит и зачем нужна область индекса (staging area)?

Коммит — это сохранённый снимок проекта с автором, датой, сообщением и ссылкой на предыдущий коммит. Индекс — промежуточная область («корзина»), куда я командой git add кладу изменения, которые войдут в следующий коммит. Он нужен, чтобы сохранять не всё подряд, а логически законченные части (например, три отдельных коммита из задания 1.2).

2. Что такое ветка в Git?

Ветка — это подвижный указатель на коммит. Когда я делаю новый коммит, указатель текущей ветки сдвигается вперёд. Ветки позволяют работать над разными задачами отдельно, не ломая main, а потом сливать результат.

3. Что такое HEAD?

HEAD — это указатель на то место, где я сейчас нахожусь: обычно на текущую ветку, а через неё на последний коммит. Новый коммит создаётся поверх HEAD, а HEAD~1 — это коммит на шаг назад.

4. Чем git fetch отличается от git pull?

git fetch только скачивает новые коммиты с сервера и не меняет мои файлы и текущую ветку. git pull делает fetch и сразу сливает скачанное в текущую ветку.

5. Почему файл .env нельзя добавлять в репозиторий?

В .env хранятся пароли и ключи. Git хранит всю историю, поэтому даже после удаления файла секрет остаётся в старых коммитах, а репозиторий могут увидеть другие люди. Поэтому .env добавляют в .gitignore, а в репозитории хранят только безопасный шаблон .env.example без реальных значений.
