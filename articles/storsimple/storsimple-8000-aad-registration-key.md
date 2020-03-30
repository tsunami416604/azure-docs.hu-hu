---
title: Azure AD-hitelesítés a StorSimple 8000 alkalmazáshoz az Eszközkezelőben
description: A cikk bemutatja, hogyan használhatja az AAD-alapú hitelesítést a szolgáltatáshoz, hogyan hozhat létre új regisztrációs kulcsot, és hogyan végezheti el az eszközök manuális regisztrációját.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b16132c24d35ee2c9902fa2b21c44416d8376b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470904"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Az új hitelesítés használata a StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás fut a Microsoft Azure-ban, és több StorSimple-eszközhöz csatlakozik. A StorSimple Eszközkezelő szolgáltatás a mai napig egy hozzáférés-vezérlési szolgáltatást (ACS) használt a szolgáltatás StorSimple-eszközre való hitelesítéséhez. Az ACS-mechanizmus hamarosan elavulttá válik, és egy Azure Active Directory (AAD) hitelesítésváltja fel. További információt az ACS eprecációjának és az AAD-hitelesítés használatának következő hirdetményei tartalmaznak.

- [Az Azure ACS jövője az Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [A Microsoft Access Control Service közelgő módosításai](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Ez a cikk ismerteti az AAD-hitelesítés és a kapcsolódó új szolgáltatás regisztrációs kulcs és a tűzfalszabályok módosításai a StorSimple-eszközökre vonatkozó részleteit. Az ebben a cikkben található információk csak a StorSimple 8000 sorozatú eszközökre vonatkoznak.

Az AAD-hitelesítés az 5. Az AAD-hitelesítés bevezetése miatt változások történnek a következőkben:

- A tűzfalszabályok URL-mintái.
- Szolgáltatás regisztrációs kulcsa.

Ezeket a változásokat részletesen tárgyaljuk a következő szakaszokban.

## <a name="url-changes-for-aad-authentication"></a>Az AAD-hitelesítés URL-módosításai

Annak érdekében, hogy a szolgáltatás AAD-alapú hitelesítést használjon, minden felhasználónak tartalmaznia kell az új hitelesítési URL-címeket a tűzfalszabályokban.

StorSimple 8000 sorozat használata esetén győződjön meg arról, hogy a tűzfalszabályok a következő URL-címet tartalmazzák:

| URL-minta                         | Felhő | Összetevő/funkcionalitás         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |AAD hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Egyesült Államok – Államigazgatás |AAD hitelesítési szolgáltatás      |

A StorSimple 8000 sorozatú eszközök URL-mintáinak teljes listáját a [tűzfalszabályok URL-mintái című lapban](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules)található.

Ha a hitelesítési URL nem szerepel a tűzfalszabályokban az eegesztési dátumon túl, és az eszközön az 5. A felhasználóknak meg kell adniuk az új hitelesítési URL-címet. Ha az eszköz az 5. A StorSimple-eszköz minden esetben nem tudja hitelesíteni magát a szolgáltatással, és a szolgáltatás nem tud kommunikálni az eszközzel.

## <a name="device-version-and-authentication-changes"></a>Az eszköz verzió- és hitelesítési módosításai

StorSimple 8000 sorozatú eszköz használata esetén az alábbi táblázat segítségével határozza meg, hogy milyen műveletet kell végrehajtania a futtatott eszközszoftver-verzió alapján.

| Ha az eszköz fut| A következő művelet végrehajtása                                    |
|--------------------------|------------------------|
| Frissítse az 5-ös vagy újabb frissítést, és az eszköz offline állapotban van. <br> Megjelenik egy figyelmeztetés, hogy az URL nem szerepel az engedélyezési listán.|1. Módosítsa a tűzfalszabályokat úgy, hogy az tartalmazza a hitelesítési URL-címet. Lásd a [hitelesítési URL-címeket.](#url-changes-for-aad-authentication)<br>2. [Szerezd meg az AAD regisztrációs kulcsot a szolgáltatásból.](#aad-based-registration-keys)<br>3. [Csatlakozzon a StorSimple 8000 sorozatú eszköz Windows PowerShell felületéhez](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. `Redo-DeviceRegistration` A parancsmag segítségével regisztrálja az eszközt a Windows PowerShell en keresztül. Adja meg az előző lépésben kapott kulcsot.|
| Frissítse az 5-ös vagy újabb frissítést, és az eszközt online állapotba.| Semmit nem kell tenni.                                       |
| Frissítse a 4-es vagy korábbi frissítést, és az eszköz offline állapotban van. |1. Módosítsa a tűzfalszabályokat úgy, hogy az tartalmazza a hitelesítési URL-címet.<br>2. [Töltse le az 5.frissítést katalógusszerveren keresztül.](storsimple-8000-install-update-5.md#download-updates-for-your-device)<br>3. [Alkalmazza az 5.](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix)<br>4. [Szerezd meg az AAD regisztrációs kulcsot a szolgáltatásból.](#aad-based-registration-keys)<br>5. [Csatlakozzon a StorSimple 8000 sorozatú eszköz Windows PowerShell felületéhez](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. `Redo-DeviceRegistration` A parancsmag segítségével regisztrálja az eszközt a Windows PowerShell en keresztül. Adja meg az előző lépésben kapott kulcsot.|
| Frissítse a 4-es vagy korábbi verziót, és az eszköz online állapotban van. |Módosítsa a tűzfalszabályokat úgy, hogy az tartalmazza a hitelesítési URL-címet.<br> Telepítse az 5.              |
| A gyári beállítások visszaállítása az 5.      |A portál egy AAD-alapú regisztrációs kulcsot jelenít meg, miközben az eszköz régebbi szoftvereket futtat. Kövesse az előző forgatókönyv lépéseit, amikor az eszköz 4.              |

## <a name="aad-based-registration-keys"></a>AAD-alapú regisztrációs kulcsok

A StorSimple 8000 sorozatú eszközök 5. A regisztrációs kulcsok segítségével regisztrálja a StorSimple Eszközkezelő szolgáltatást az eszközhöz.

Az új AAD szolgáltatás regisztrációs kulcsai nem használhatók, ha a 4.
Ebben az esetben újra kell generálnia a szolgáltatás regisztrációs kulcsát. A kulcs újragenerálása után az új kulcs az összes további eszköz regisztrálására szolgál. A régi kulcs már nem érvényes.

- Az új AAD regisztrációs kulcs 3 nap után lejár.
- Az AAD regisztrációs kulcsok csak az 5.
- Az AAD regisztrációs kulcsok hosszabbak, mint a megfelelő ACS regisztrációs kulcsok.

Hajtsa végre az alábbi lépéseket az AAD-szolgáltatás regisztrációs kulcsának létrehozásához.

#### <a name="to-generate-the-aad-service-registration-key"></a>Az AAD szolgáltatás regisztrációs kulcsának létrehozása

1. A **StorSimple Eszközkezelőben**nyissa meg a **Felügyeleti &gt; ** **kulcsok lehetőséget.** A keresősáv segítségével is kereshet _a Kulcsok kereséséhez._
    
2. Kattintson **a Kulcs létrehozása gombra.**

    ![Kattintson az újragenerálás gombra](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Másolja az új kulcsot. A régebbi kulcs nem fog működni.

    ![Regenerálás megerősítése](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Ha egy StorSimple cloud appliance a StorSimple 8000 sorozatú eszköz regisztrált szolgáltatás, ne hozzon létre regisztrációs kulcsot, amíg a létrehozás folyamatban van. Várja meg, amíg a létrehozás befejeződik, majd hozza létre a regisztrációs kulcsot.

## <a name="next-steps"></a>További lépések

* További információ a [StorSimple 8000 sorozatú eszköz](storsimple-8000-deployment-walkthrough-u2.md)telepítéséről.

