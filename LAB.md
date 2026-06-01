# LAB — день 5

## Базовая задача — `01-history-detective`

### Часть 1 — pickaxe и blame

**Коммит с паролем БД (`wp_s3cr3t`):**

- Хеш: `ddaed7aff3e433beaa648b8544a0d9f7a15e287e`
- Что изменилось: `Разработчик добавил в плейбук скачивание архива WP и его деплой, при этом в конфиге захардкожены данные для доступа к БД`

```bash
# git log -S 'wp_s3cr3t' -p
commit ddaed7aff3e433beaa648b8544a0d9f7a15e287e
Author: Git Bootcamp Starter <bootcamp@example.com>
Date:   Thu May 28 08:49:38 2026 +0200

    feat(wordpress): add WordPress download and configuration

diff --git a/vars/main.yml b/vars/main.yml
index 9d8fcb3..96e92b8 100644
--- a/vars/main.yml
+++ b/vars/main.yml
@@ -6,3 +6,5 @@ wp_db_user: wp_user
 php_version: "8.2"
 nginx_client_max_body_size: "10m"
 nginx_keepalive_timeout: 30
+db_password: "wp_s3cr3t"
+wordpress_download_url: "https://wordpress.org/latest.tar.gz"
```

**Строка `client_max_body_size` (blame):**

- Автор из `git blame`: `Git Bootcamp Starter`
- Хеш коммита: `c93c5315e5803ca6f36f6871b2efd6a1f7fffb55`
- Кратко, что сделал этот коммит: `Разработчик захардкодил параметры nginx в шаблон вместо того, чтоб записать их в файл с переменными`

```bash
# git blame -L '/client_max_body_size/,+3' templates/nginx.conf.j2
# с учетом, что файл небольшой - можно просто git blame templates/nginx.conf.j2
c93c5315 (Git Bootcamp Starter 2026-05-28 08:49:38 +0200 5)     client_max_body_size 64m;
c93c5315 (Git Bootcamp Starter 2026-05-28 08:49:38 +0200 6)     keepalive_timeout 65;
3324c769 (Git Bootcamp Starter 2026-05-28 08:49:38 +0200 7) 
```

### Часть 2 — reset --hard и reflog

- Хеш HEAD **до** `reset --hard HEAD~2`: `8bf4ed7f8ac200102793075b5281b3e6ac9e41ca`
- Хеш из `reflog`, на который откатывались при восстановлении: `ddaed7aff3e433beaa648b8544a0d9f7a15e287e`
- Полная команда восстановления: `git reset --hard 8bf4ed7`
(также можно ориентироваться на origin/main из git adog, но репа должна быть запушена после разворачивания)

```bash
# git reflog | head -10
ddaed7a (HEAD -> main) HEAD@{0}: reset: moving to HEAD~2
8bf4ed7 (origin/main) HEAD@{1}: clone: from /home/osboxes/slurm_git_intensive/git-bootcamp/hometasks/day5/
starter/wordpress-deploy.bundle  
```

### Часть 3 — revert

- Хеш отменённого коммита (`fix(nginx): update nginx template defaults and keepalive_timeout`): `c93c5315e5803ca6f36f6871b2efd6a1f7fffb55`
- Хеш отменяющего коммита (revert): `d73d888a57df9a55d7cbfd0d1a2b6d6d07b435fc`
- Заголовок revert-коммита (как в `git log --oneline -1` после revert): `Revert "fix(nginx): update nginx template defaults and keepalive_timeout"`
- Хеш security-коммита после ротации пароля: `2863875d4e711fd622ebc28d77c5fb41251f2f46`
- Первая строка body этого security-коммита: `DB password was rotated.`
- Как теперь выглядит строка в `vars/main.yml` вместо открытого пароля: `# db_password: ""`

**Revert vs reset --hard: (почти своими словами):**

- Для публичных веток изменения лучше откатывать через revert - так у коллег не сломается история, не потеряются их локальные изменения.
- Для приватной ветки допустимо **до того, как запушили** откатить ломающие изменения (пару коммитов максимум) через reset --hard. Но как показала практика, легко опечататься и в панике наворотить с репой плохое, особенно при незнании reflog. :)

---

TODO: задачи со звездочками, когда почувствую Силу
