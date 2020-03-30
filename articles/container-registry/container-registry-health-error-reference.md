---
title: Hibahivatkozás az állapotellenőrzésekhez
description: Hibakódok és az Azure Container Registry az acr check-health diagnosztikai parancsának futtatásával talált problémák lehetséges megoldásai
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289141"
---
# <a name="health-check-error-reference"></a>Állapot-ellenőrzés hibahivatkozása

Az alábbiakban az az [acr check-health][az-acr-check-health] parancs által visszaadott hibakódok részleteit közöljük. Minden egyes hiba esetében a lehetséges megoldások vannak felsorolva.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Ez a hiba azt jelenti, hogy a CLI Docker-ügyfele nem található. Ennek eredményeképpen a következő további ellenőrzések nem futnak: a Docker-verzió megkeresése, a Docker démonállapotának kiértékelése és a Docker-lekéréses parancs futtatása.

*Lehetséges megoldások*: Telepítse a Docker-ügyfelet; add hozzá a Docker elérési útját a rendszerváltozókhoz.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Ez a hiba azt jelenti, hogy a Docker démon állapota nem érhető el, vagy hogy nem érhető el a CLI használatával. Ennek eredményeképpen a Docker-műveletek `docker login` `docker pull`(például és ) nem érhetők el a CLI-n keresztül.

*Lehetséges megoldások:* Indítsa újra a Docker démont, vagy ellenőrizze, hogy megfelelően van-e telepítve.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Ez a hiba azt jelenti, hogy `docker --version`a CLI nem tudta futtatni a parancsot .

*Lehetséges megoldások:* Próbálja meg manuálisan futtatni a parancsot, győződjön meg arról, hogy a legújabb CLI-verzióval rendelkezik, és vizsgálja meg a hibaüzenetet.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Ez a hiba azt jelenti, hogy a CLI nem tudott mintát venni a környezetbe.

*Lehetséges megoldások:* Ellenőrizze, hogy a lemezkép lehúzásához szükséges összes összetevő megfelelően fut-e.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Ez a hiba azt jelenti, hogy a Helm-ügyfelet a CLI nem találta, ami kizárja a többi Helm-műveletet.

*Lehetséges megoldások:* Ellenőrizze, hogy a Helm ügyfél telepítve van-e, és hogy az elérési útja hozzá van-e adva a rendszerkörnyezeti változókhoz.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Ez a hiba azt jelenti, hogy a CLI nem tudta meghatározni a telepített Helm verziót. Ez akkor fordulhat elő, ha az Azure CLI-verzió (vagy ha a Helm verzió) használatos elavult.

*Lehetséges megoldások:* Frissítsen a legújabb Azure CLI-verzióra vagy az ajánlott Helm-verzióra; futtassa a parancsot manuálisan, és vizsgálja meg a hibaüzenetet.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Ez a hiba azt jelenti, hogy az adott rendszerleíró adatbázis bejelentkezési kiszolgálójának DNS-e pingelve lett, de nem válaszolt, ami azt jelenti, hogy nem érhető el. Ez bizonyos kapcsolódási problémákat jelezhet. Azt is megteheti, hogy a beállításjegyzék nem létezik, a felhasználó nem rendelkezik az engedélyekkel a beállításjegyzékben (a bejelentkezési kiszolgáló megfelelő beolvasásához), vagy a célbeállítás-beállításjegyzék az Azure CLI-ben használttól eltérő felhőben van.

*Lehetséges megoldások*: A kapcsolat ellenőrzése; ellenőrizze a beállításjegyzék helyesírását, és létezik ilyen beállításjegyzék; ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel, és hogy a beállításjegyzék felhője megegyezik-e az Azure CLI-ben használt adatokkal.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ez a hiba azt jelenti, hogy az adott beállításjegyzék kihívásvégpontja 403 Tiltott HTTP-állapottal válaszolt. Ez a hiba azt jelenti, hogy a felhasználók nem férnek hozzá a rendszerleíró adatbázishoz, valószínűleg egy virtuális hálózati konfiguráció miatt. A jelenleg konfigurált tűzfalszabályok `az acr show --query networkRuleSet --name <registry>`megtekintéséhez futtassa a futtassa a futtassa a programot.

*Lehetséges megoldások:* Távolítsa el a virtuális hálózati szabályokat, vagy adja hozzá az aktuális ügyfél IP-címét az engedélyezett listához.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Ez a hiba azt jelenti, hogy a célbeállításjegyzék kihívásvégpontja nem adott ki kihívást.

*Lehetséges megoldások*: Próbálkozzon újra egy idő után. Ha a hiba továbbra is https://aka.ms/acr/issuesfennáll, nyisson meg egy problémát a következő helyen:

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ez a hiba azt jelenti, hogy a célbeállítás-beállításjegyzék kihívása kihívást jelentett, de a beállításjegyzék nem támogatja az Azure Active Directory-hitelesítést.

*Lehetséges megoldások:* Próbálkozzon más módon a hitelesítéshez, például rendszergazdai hitelesítő adatokkal. Ha a felhasználóknak az Azure Active Directory https://aka.ms/acr/issueshasználatával kell hitelesíteniük magukat, nyissa meg a problémát a> területen.

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ez a hiba azt jelenti, hogy a rendszerleíró adatbázis bejelentkezési kiszolgálója nem válaszolt frissítési jogkivonattal, ezért a rendszer megtagadta a célbeállításjegyzékhez való hozzáférést. Ez a hiba akkor fordulhat elő, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzékben, vagy ha az Azure CLI felhasználói hitelesítő adatai elavultak.

*Lehetséges megoldások:* Ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzékben; futtatja `az login` az engedélyek, jogkivonatok és hitelesítő adatok frissítéséhez.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ez a hiba azt jelenti, hogy a rendszerleíró adatbázis bejelentkezési kiszolgálója nem válaszolt hozzáférési jogkivonattal, így a rendszer megtagadta a célbeállításjegyzékhez való hozzáférést. Ez a hiba akkor fordulhat elő, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzékben, vagy ha az Azure CLI felhasználói hitelesítő adatai elavultak.

*Lehetséges megoldások:* Ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzékben; futtatja `az login` az engedélyek, jogkivonatok és hitelesítő adatok frissítéséhez.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Ez a hiba azt jelenti, hogy az ügyfél nem tudott biztonságos kapcsolatot létesíteni a tároló beállításjegyzékével. Ez a hiba általában akkor fordul elő, ha proxykiszolgálót futtat vagy használ.

*Lehetséges megoldások*: További információ a proxy mögött imitálásról [itt található.](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy)

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Ez a hiba azt jelenti, hogy a CLI nem találta az adott beállításjegyzék bejelentkezési kiszolgálóját, és nem található alapértelmezett utótag az aktuális felhőhöz. Ez a hiba akkor fordulhat elő, ha a beállításjegyzék nem létezik, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzékben, ha a beállításjegyzék felhője és a jelenlegi Azure CLI-felhő nem egyezik, vagy ha az Azure CLI verziója elavult.

*Lehetséges megoldások*: Ellenőrizze, hogy a helyesírás helyes-e, és hogy a beállításjegyzék létezik-e; ellenőrzi, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzékben, és hogy a rendszerleíró adatbázis és a CLI-környezet felhői egyeznek-e; frissítse az Azure CLI-t a legújabb verzióra.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Ez a hiba azt jelenti, hogy a CLI nem kompatibilis a Docker/Notary jelenleg telepített verziójával. Próbálja meg a Notary.exe verzióját a 0.6.0-nál korábbi verzióra visszaállítani a Docker-telepítés közjegyzői ügyfelének manuális cseréjével a probléma megoldásához.

## <a name="next-steps"></a>További lépések

A beállításjegyzék állapotának ellenőrzésére szolgáló lehetőségekről [az Azure-tárolók beállításjegyzékének ellenőrzése című témakörben található.](container-registry-check-health.md)

Tekintse meg a [gyakran](container-registry-faq.md) ismételt kérdéseket és az Azure Container Registry egyéb ismert problémáit.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
