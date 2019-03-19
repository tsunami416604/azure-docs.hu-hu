---
title: Új hitelesítés használata a StorSimple 8000-es Device Manager szolgáltatás az Azure-ban |} A Microsoft Docs
description: A szolgáltatás az AAD-alapú hitelesítést használ, hozzon létre új regisztrációs kulcsot, és az eszközök manuális regisztrációjához módját ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 01d36188c1684eae8303cb20ba0fd0c708ff91ba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079912"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>A StorSimple az új hitelesítés használatára

## <a name="overview"></a>Áttekintés

A StorSimple-Eszközkezelő szolgáltatás a Microsoft Azure-ban fut, és több StorSimple-eszközhöz csatlakozik. Dátum, a StorSimple-Eszközkezelő szolgáltatás rendelkezik egy Access Control service (ACS) hitelesítésére használja a szolgáltatást, hogy a StorSimple-eszköz. Az ACS-mechanizmus hamarosan elavulttá fog, és a rendszer váltotta fel az Azure Active Directory (AAD) hitelesítéshez. További információkért nyissa meg a következő közleményeket az ACS-et és AAD-hitelesítés használatát.

- [A jövő az Azure Access Control Service az Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [A Microsoft Access Control Service küszöbönálló változásait](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Ez a cikk ismerteti az AAD-hitelesítés és a kapcsolódó új Szolgáltatásregisztrációs kulcs és a StorSimple-eszközökre vonatkozik, mint a tűzfalszabályok módosításai részleteit. Ebben a cikkben található információk csak a StorSimple 8000 sorozatú eszközök alkalmazható.

5-ös vagy újabb frissítést futtató StorSimple 8000 sorozatú eszköz az AAD-hitelesítéssel történik. Az AAD-hitelesítés bevezetésével miatt bekövetkező változások:

- URL-mintákra tűzfalszabályokat.
- Szolgáltatásregisztrációs kulcs.

Az alábbi szakaszok részletesebben ismertetik ezeket a módosításokat.

## <a name="url-changes-for-aad-authentication"></a>AAD-hitelesítés URL-cím módosításai

Győződjön meg arról, hogy a szolgáltatás használja-e az AAD-alapú hitelesítés, a minden felhasználó tartalmaznia kell az új hitelesítési URL-címeket be a tűzfalszabályok.

A StorSimple 8000 sorozat használata esetén győződjön meg arról, hogy a tűzfalszabályok a következő URL-címet tartalmaz:

| Az URL-minta                         | Felhő | Component/Functionality         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |AAD-hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Az USA kormányzata |AAD-hitelesítési szolgáltatás      |

Az URL-cím teljes listáját a StorSimple 8000 sorozatú eszközök mintáit, Ugrás [URL-mintákra vonatkozó tűzfalszabályok](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

A hitelesítési URL-cím nem része a tűzfalszabályok túli elavulásának dátuma, és az eszköz fut-e Update 5, akkor a felhasználók egy URL-cím riasztás megjelenítéséhez. A felhasználóknak kell az új hitelesítési URL-címét. Ha az eszköz 5. frissítés előtti verziót futtat, a felhasználók megtekintéséhez a szívverés miatti riasztást. Minden esetben a StorSimple-eszköz nem tudja hitelesíteni a szolgáltatással és a szolgáltatás nem tud kommunikálni az eszköz.

## <a name="device-version-and-authentication-changes"></a>Eszköz verziója és a hitelesítési módosítások

A StorSimple 8000 sorozatú eszköz használata esetén a következő táblázat segítségével határozza meg, milyen lépéseket kell tennie az eszköz szoftververzióján futtatja alapján.

| Ha az eszköz fut.| A következő művelet elvégzése                                    |
|--------------------------|------------------------|
| Update 5 vagy újabb verzió és az eszköz offline állapotban. <br> Megjelenik egy értesítés, hogy az URL-je nem szerepel az engedélyezési listán.|1. Módosítsa a tűzfal-szabályokat, a hitelesítési URL-címét. Lásd: [hitelesítési URL-címek](#url-changes-for-aad-authentication).<br>2. [Az AAD-regisztrációs kulcs beolvasása a szolgáltatástól](#aad-based-registration-keys).<br>3. [A Windows PowerShell felületet a StorSimple 8000 sorozatú eszköz csatlakozni](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Használat `Redo-DeviceRegistration` parancsmagot, hogy regisztrálja az eszközt a Windows Powershellen keresztül. Adja meg az előző lépésben kapott kulcsot.|
| Update 5 vagy újabb verzió és az eszközt.| Nincs szükség műveletre.                                       |
| Update 4-es vagy korábbi és az eszköz offline állapotban. |1. Módosítsa a tűzfal-szabályokat, a hitelesítési URL-címét.<br>2. [5. frissítés letöltése katalógus kiszolgálón keresztül](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [5. frissítés alkalmazása a gyorsjavítás módszerrel](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Az AAD-regisztrációs kulcs beolvasása a szolgáltatástól](#aad-based-registration-keys).<br>5. [A Windows PowerShell felületet a StorSimple 8000 sorozatú eszköz csatlakozni](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Használat `Redo-DeviceRegistration` parancsmagot, hogy regisztrálja az eszközt a Windows Powershellen keresztül. Adja meg az előző lépésben kapott kulcsot.|
| Update 4-es vagy korábbi és az eszköz online állapotban. |Módosítsa a tűzfal-szabályokat, a hitelesítési URL-címét.<br> 5. frissítés telepítése az Azure Portalon keresztül.              |
| A gyári alaphelyzetbe állítása előtt Update 5 verzióra.      |A portál megjeleníti egy AAD-alapú regisztrációs kulcsot, az eszköz régebbi szoftverek futtatása közben. Kövesse a lépéseket, ha az eszköz fut Update 4-es vagy korábbi az előző forgatókönyvben.              |

## <a name="aad-based-registration-keys"></a>AAD-alapú regisztrációs kulcsokat

5. frissítés kezdve a StorSimple 8000 sorozat eszközeire, új kulcsokkal AAD-alapú regisztráció. A regisztrációs kulcs használatával regisztrálja az eszközt a StorSimple-Eszközkezelő szolgáltatásban.

Az AAD szolgáltatás új regisztrációs kulcsok használata a StorSimple 8000 sorozatú eszköz Update 4-es vagy korábbi (tartalmaz egy régebbi eszközjogkivonat most) futtatása nem használható.
Ebben a forgatókönyvben a szolgáltatás regisztrációs kulcsának újragenerálása kell. Ha újragenerálja a kulcsot, az új kulcs szolgál az ezt követő eszközök regisztrálásához. A régi kulcsot már nem érvényes.

- Az aad-ben új regisztrációs kulcs 3 nap után lejár.
- Az aad-ben regisztrációs kulcsokat, munkahelyi csak a StorSimple 8000 sorozatú eszközök Update 5 vagy újabb.
- Az AAD-regisztrációs kulcsok hosszabbak, mint a megfelelő ACS-regisztrációs kulcsokat.

A következő lépésekkel hozzon létre egy AAD szolgáltatás regisztrációs kulcsát.

#### <a name="to-generate-the-aad-service-registration-key"></a>Az AAD Szolgáltatásregisztrációs kulcs létrehozása

1. A **StorSimple-Eszközkezelő**, lépjen a **felügyeleti &gt;**  **kulcsok**. Keresendő is használhatja a keresősávba _kulcsok_.
    
2. Kattintson a **kulcs generálása**.

    ![Kattintson a fiókkulcs újbóli létrehozása](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Másolja ki az új kulccsal. A régebbi kulcs nem fog működni.

    ![Erősítse meg a fiókkulcs újbóli létrehozása](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Ha a szolgáltatás a StorSimple 8000 sorozatú eszköz regisztrálva a StorSimple Cloud Appliance hoz létre, nem hoznak létre regisztrációs kulcsát a létrehozása közben. Várjon, amíg a létrehozás befejeződését, majd hozza létre a regisztrációs kulcsot.

## <a name="next-steps"></a>További lépések

* További információ a központi telepítése [StorSimple 8000 sorozatú eszköz](storsimple-8000-deployment-walkthrough-u2.md).

