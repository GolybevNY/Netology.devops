Домашнее задание к занятию «2.4. Инструменты Git»

1. Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.
>git show -s --pretty=oneline aefea
aefead2207ef7e2aa5dc81a34aedf0cad4c32545 Update CHANGELOG.md

2. Какому тегу соответствует коммит 85024d3?
>git show -s --pretty=medium 85024d3
commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)
Author: tf-release-bot <terraform@hashicorp.com>
Date:   Thu Mar 5 20:56:10 2020 +0000

    v0.12.23

3. Сколько родителей у коммита b8d720? Напишите их хеши.
>git show -s --format=%p b8d720
56cd7859e 9ea88f22f

4. Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.
>git log v0.12.23..v0.12.24 --pretty=format:"%C(auto)%h %s, %ad, %an"
33ff1c03b v0.12.24, Thu Mar 19 15:04:05 2020 +0000, tf-release-bot
b14b74c49 [Website] vmc provider links, Tue Mar 10 08:59:20 2020 -0700, Chris Griggs
3f235065b Update CHANGELOG.md, Thu Mar 19 10:39:31 2020 -0400, Alisdair McDiarmid
6ae64e247 registry: Fix panic when server is unreachable, Thu Mar 19 10:20:10 2020 -0400, Alisdair McDiarmid
5c619ca1b website: Remove links to the getting started guide's old location, Wed Mar 18 12:30:20 2020 -0700, Nick Fagerlund
06275647e Update CHANGELOG.md, Wed Mar 18 10:57:06 2020 -0400, Alisdair McDiarmid
d5f9411f5 command: Fix bug when using terraform login on Windows, Tue Mar 17 13:21:35 2020 -0400, Alisdair McDiarmid
4b6d06cc5 Update CHANGELOG.md, Tue Mar 10 12:04:50 2020 -0400, Pam Selle
dd01a3507 Update CHANGELOG.md, Thu Mar 5 16:32:43 2020 -0500, Kristin Laemmert
225466bc3 Cleanup after v0.12.23 release, Thu Mar 5 21:12:06 2020 +0000, tf-release-bot

5. Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).
>git log -S "func providerSource(" --pretty="format:%C(auto)%h (%s, %ad) %an"
8c928e835 (main: Consult local directories as potential mirrors of providers, Thu Apr 2 18:04:39 2020 -0700) Martin Atkins

6. Найдите все коммиты в которых была изменена функция globalPluginDirs.
>git grep -p "globalPluginDirs("
commands.go=func initCommands(
commands.go:            GlobalPluginDirs: globalPluginDirs(),
commands.go=func credentialsSource(config *cliconfig.Config) (auth.CredentialsSource, error) {
commands.go:    helperPlugins := pluginDiscovery.FindPlugins("credentials", globalPluginDirs())
plugins.go=import (
plugins.go:func globalPluginDirs() []string {	

>git log -L :globalPluginDirs:plugins.go --oneline
	78b122055 Remove config.go and update things using its aliases
	52dbf9483 keep .terraform.d/plugins for discovery
	41ab0aef7 Add missing OS_ARCH dir to global plugin paths
	66ebff90c move some more plugin search path logic to command
	8364383c3 Push plugin discovery down into command package

7. Кто автор функции synchronizedWriters?
>git log -S "synchronizedWriters(" --pretty="format:%C(auto)%h (%s, %ad) %an"
bdfea50cc (remove unused, Mon Nov 30 18:02:04 2020 -0500) James Bardin
fd4f7eb0b (remove prefixed io, Wed Oct 21 13:06:23 2020 -0400) James Bardin
5ac311e2a (main: synchronize writes to VT100-faker on Windows, Wed May 3 16:25:41 2017 -0700) Martin Atkins 	
	Выбран самый ранний коммит. Ответ: Martin Atkins
