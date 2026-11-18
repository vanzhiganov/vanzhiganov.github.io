---
layout: post
title: "Как оформлять патчи в spec-файле для сборки RPM"
subtitle: ""
tags: [linux, koji]
---

## Предпосылки

Часто при сопровождении rpm-пакетов приходится применять патчи.
Задачи, которые решает патчине пакетов могут быть разными:
- добавляет точечное исправление ошибки в коде без ожидания новой версии upstream
- переносит важное исправление из новой версии в старую поддерживаемую версию пакета (backport)
- адаптация под конкретный дистрибутив, меняет пути установки, системные вызовы, зависимости, поведение под RHEL/ALT/другой дистрибутив
- Воспроизводимость сборки - отделяет неизменённые upstream-исходники от изменений мейнтейнера

**Патчи, это хорошо, но что делать, если патчей становится сильно много?**

Тут я выделяю одну частую сложность при ведении патчей в пакетах, а именно:
отсутствие описания и отсылок на обсуждение или решение о необходимости патчинга.

**Как это выглядит в жизни** на примере spec-файла openssl:

```
...
# Bug fixes
Patch23: openssl-1.0.2c-default-paths.patch
Patch24: openssl-1.0.2a-issuer-hash.patch
# Functionality changes
Patch33: openssl-1.0.0-beta4-ca-dir.patch
Patch34: openssl-1.0.2a-x509.patch
Patch35: openssl-1.0.2a-version-add-engines.patch
...
Patch98: openssl-1.0.2k-long-hello.patch
Patch99: openssl-1.0.2k-fips-randlock.patch
Patch106: openssl-1.0.2k-rsa-check.patch
# Backported fixes including security fixes
Patch80: openssl-1.0.2e-wrap-pad.patch
Patch81: openssl-1.0.2a-padlock64.patch
Patch82: openssl-1.0.2i-trusted-first-doc.patch
...
Patch116: openssl-1.0.2k-cve-2022-0778.patch
Patch117: openssl-1.0.2k-cve-2022-1292.patch
Patch118: openssl-1.0.2k-cve-2022-2068.patch
...
```

## Что я пробую решить

**1. Исправления не применяются разработчиком** (по разным причинам)

В комментарии указываю ссылку на обсуждение и ссылку (или внутренний номер)

```
# downstream patch - issue #12345 {description}
# rejected upstream: https://openssl.org/...
Patch1:            0001-patch-description.patch
```

**2. Применяем патч из upstream**

Указываем URL с обсуждением

```
# upstream patch - CVE-2000-0000 fixing ... security issue - https://redmine.homepage.com/issue/12345
Patch1:            0001-patch-description.patch
```

**3. Применяем патч из upstream**

Указываем URL с обсуждением и URL коммита 

```
# https://redmine.homepage.com/issue/12345
# upstream patch - https://github.com/repo/repo/commit/ab3a1a451456ea33d01b34561ab35614
Patch1:           0001-commit-description.patch
```

## Заключение

Оформление патчей в spec-файл указанным образом обязывает сопровождающего на дополнительную работу.
Однако, с течением времени, данный подход позволит быстро восстанавливать контекст проделанной работы в будущем и
более быстро принимать решения.
