---
title: Állapot-ellenőrzése – az Azure Container Registry hiba referenciája
description: Hibakódok és lehetséges megoldásokat az Azure Container Registryben az acr jelölőnégyzet-állapot diagnosztikai parancs futtatásával talált problémák
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555110"
---
# <a name="health-check-error-reference"></a>Állapot ellenőrzése hibás hivatkozás

Az alábbiakban által visszaadott hibakódok részleteit a [az acr jelölőnégyzet-állapot][az-acr-check-health] parancsot. Minden egyes hibához a lehetséges megoldásokról jelennek meg.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Ez a hiba, az azt jelenti, hogy a CLI Docker-ügyfél nem található. Ennek eredményeképpen nem futnak a következő további ellenőrzések: Docker-verzió, Docker kiértékelése démon állapot keresése és futtatása a Docker pull parancsot.

*A lehetséges megoldások*: Docker-ügyfél; telepítése Docker-elérési út hozzáadása a rendszerváltozók.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Ez a hiba azt jelenti, hogy a Docker-démon állapota nem érhető el, vagy hogy azt nem érhető el a parancssori felület használatával. Emiatt, Docker-műveletek (például `docker login` és `docker pull`) érhetők el a parancssori felületén keresztül.

*A lehetséges megoldások*: Indítsa újra a Docker-démont, vagy ellenőrizze, hogy helyesen van-e telepítve.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Ez a hiba, az azt jelenti, hogy a CLI nem volt képes a parancs futtatásához `docker --version`.

*A lehetséges megoldások*: Próbálja ki a következő parancs futtatásával manuálisan, ellenőrizze, hogy a CLI legújabb verzióját, és vizsgálja meg a hibaüzenetet.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Ez a hiba, az azt jelenti, hogy a CLI nem tudta lekérni egy képet a környezetben.

*A lehetséges megoldások*: Ellenőrizze, hogy az összes összetevő szükséges képet betölteni megfelelően futnak-e.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Ez a hiba azt jelenti, hogy a Helm-ügyfél nem található a parancssori felület, amely kizárja a Helm-műveletnél.

*A lehetséges megoldások*: Ellenőrizze, hogy a Helm-ügyfél telepítve van-e, valamint, hogy az elérési útja bekerül a rendszerszintű környezeti változókat.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Ez a hiba, az azt jelenti, hogy a CLI nem tudta meghatározni a Helm-verzió van telepítve. Ez akkor fordulhat elő, ha az Azure CLI verziójának (vagy ha a Helm-verzió) használt elavult.

*A lehetséges megoldások*: Azure CLI legújabb verzióját vagy az ajánlott Helm verzió frissítése manuálisan futtassa a parancsot, és vizsgálja meg a hibaüzenetet.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Ez a hiba azt jelenti, hogy a DNS-ÉT az adott adatbázis bejelentkezési kiszolgálója volt pingkérést küldött, de nem válaszolt, ami azt jelenti, hogy nem érhető el. Ez azt jelezheti kapcsolódási problémák. Azt is megteheti a beállításjegyzék nem létezik, a felhasználó előfordulhat, hogy nincs engedélye a a beállításjegyzék (lekérdezni a bejelentkezési kiszolgáló megfelelően), vagy a tároló-beállításjegyzék, mint az Azure CLI-ben használt egy másik felhőben.

*A lehetséges megoldások*: Ellenőrizze a csatlakozásukat; a beállításjegyzék helyesírás-ellenőrzése, és a beállításjegyzék létezik; Győződjön meg arról, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel, és, hogy a tárolójegyzék felhőalapú megegyezik az Azure CLI-ben használt.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ez a hiba, az azt jelenti, hogy az adott beállításjegyzékhez a kérdés végpont válaszolt egy 403 Tiltott HTTP-állapot. Ez a hiba, az azt jelenti, hogy felhasználók nincs hozzáférése a regisztrációs adatbázisba, valószínűleg egy virtuális hálózati konfiguráció miatt.

*A lehetséges megoldások*: Távolítsa el a virtuális hálózati szabályok, vagy a jelenlegi ügyfél IP-cím hozzáadása az engedélyezett listára.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Ez a hiba, az azt jelenti, hogy a tároló-beállításjegyzék challenge végpontja nem hajtotta végre kihívást.

*A lehetséges megoldások*: Próbálkozzon újra egy kis idő múlva. Ha a hiba nem szűnik meg, nyissa meg a probléma https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ez a hiba azt jelenti, hogy a tároló-beállításjegyzék challenge végpontja kihívást, de az a beállításjegyzék nem támogatja az Azure Active Directory-hitelesítést.

*A lehetséges megoldások*: Próbálja ki a másik mód a hitelesítésre, például rendszergazdai hitelesítő adataival. Felhasználók hitelesítése az Azure Active Directory használatával kell, ha nyissa meg a problémát, https://aka.ms/acr/issues.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ez a hiba, az azt jelenti, hogy a tárolójegyzék bejelentkezési kiszolgálójának nem válaszolt frissítési jogkivonatok, így a tároló-beállításjegyzék való hozzáférés megtagadva. Ez a hiba akkor fordulhat elő, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzék, vagy ha a felhasználói hitelesítő adatok az Azure CLI elavult.

*A lehetséges megoldások*: Győződjön meg arról, ha a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzék; Futtatás `az login` engedélyeket, a jogkivonatok és a hitelesítő adatok frissítéséhez.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ez a hiba azt jelenti, hogy, hogy a tárolójegyzék bejelentkezési kiszolgálójának nem válaszolt egy hozzáférési jogkivonattal, hogy a tároló-beállításjegyzék a hozzáférés meg lett tagadva. Ez a hiba akkor fordulhat elő, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzék, vagy ha a felhasználói hitelesítő adatok az Azure CLI elavult.

*A lehetséges megoldások*: Győződjön meg arról, ha a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzék; Futtatás `az login` engedélyeket, a jogkivonatok és a hitelesítő adatok frissítéséhez.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Ez a hiba azt jelenti, hogy a CLI nem találja a megadott beállításkulcs értékét a bejelentkezési kiszolgáló, nem alapértelmezett utótag nem található a jelenlegi felhőt. Ez a hiba akkor fordulhat elő, ha a beállításjegyzék nem létezik, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzék Ha a tárolójegyzék felhőbeli és a jelenlegi Azure CLI-felhő nem egyezik, vagy ha az Azure CLI verziója elavult.

*A lehetséges megoldások*: Győződjön meg arról, hogy helyesen szerepel-e a helyesírás-ellenőrzés, és arról, hogy létezik a beállításjegyzék; Győződjön meg arról, hogy a felhasználó a megfelelő engedélyekkel rendelkezik a beállításjegyzékben, és a felhők a beállításjegyzék és a parancssori felületi környezetben megfelelő; Azure CLI frissítése a legújabb verzióra.

## <a name="next-steps"></a>További lépések

A beállításjegyzék állapotának ellenőrzéséhez lehetőségekről [egy Azure container registryt állapotát](container-registry-check-health.md).

Tekintse meg a [– gyakori kérdések](container-registry-faq.md) vonatkozó gyakori kérdések és ismert problémákkal kapcsolatban az Azure Container Registrybe.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
