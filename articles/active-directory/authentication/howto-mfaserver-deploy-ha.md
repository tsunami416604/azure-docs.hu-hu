---
title: Magas rendelkezésre állás az Azure MFA Server számára – Azure Active Directory
description: Az Azure többtényezős hitelesítési kiszolgáló több példányát olyan konfigurációkban telepítse, amelyek magas rendelkezésre állást biztosítanak.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7b5f6bef5358acf0709f994b85215e505fa4db
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653379"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Az Azure többtényezős hitelesítési kiszolgálójának konfigurálása magas rendelkezésre állásra

Az Azure Server MFA-telepítésével való magas rendelkezésre állás eléréséhez több MFA-kiszolgálót kell telepítenie. Ez a szakasz a terhelés-kiegyensúlyozott kialakítással kapcsolatos információkat tartalmaz az Azure MFS-kiszolgáló üzembe helyezésében a magas rendelkezésre állású célok eléréséhez.

> [!IMPORTANT]
> 2019. július 1-jéig a Microsoft a továbbiakban nem ajánlja fel az MFA Server alkalmazást az új telepítésekhez. Azoknak az új ügyfeleknek, akik többtényezős hitelesítést szeretnének megkövetelni a felhasználóiktól, felhőalapú Azure többtényezős hitelesítést kell használniuk. Azok a meglévő ügyfelek, akik július 1-je előtt aktiválták az MFA-kiszolgálót, a szokásos módon letölthetik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon létrehozhatják az aktiválási hitelesítő adatokat.

## <a name="mfa-server-overview"></a>MFA-kiszolgáló – áttekintés

Az Azure MFA Server szolgáltatásarchitektúra több összetevőből áll, ahogy az az alábbi ábrán látható:

 ![MFA-kiszolgálóarchitektúra összetevői](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Az MFA-kiszolgáló olyan Windows Server, amelyen telepítve van az Azure többtényezős hitelesítési szoftver. Az MFA-kiszolgáló példányt az MFA-szolgáltatásnak aktiválnia kell az Azure-ban a működéshez. A helyszínen több MFA-kiszolgáló is telepíthető.

Az első telepített MFA-kiszolgáló a fő MFA-kiszolgáló az Azure MFA-szolgáltatás által alapértelmezés szerint történő aktiváláskor. A fő MFA-kiszolgáló rendelkezik a PhoneFactor.pfdata adatbázis írható másolatával. Az MFA-kiszolgáló példányainak későbbi telepítéseit beosztottaknak nevezzük. Az MFA-beosztottak a PhoneFactor.pfdata adatbázis replikált, írásvédett másolatával rendelkeznek. Az MFA-kiszolgálók távoli eljáráshívás (RPC) használatával replikálják az adatokat. Az adatok replikálásához minden MFA-severs-nek együttesen tartományhoz vagy önállónak kell lennie.

Az MFA-főkiszolgáló és az alárendelt MFA-kiszolgálók is kommunikálnak az MFA-szolgáltatással, ha kétfaktoros hitelesítésre van szükség. Ha például egy felhasználó megpróbál hozzáférni egy kétfaktoros hitelesítést igénylő alkalmazáshoz, a felhasználót először egy identitásszolgáltató, például az Active Directory (AD) hitelesíti.

Az AD-vel való sikeres hitelesítést követően az MFA-kiszolgáló kommunikál az MFA-szolgáltatással. Az MFA-kiszolgáló megvárja az MFA-szolgáltatás értesítését, amely engedélyezi vagy megtagadja a felhasználó hozzáférését az alkalmazáshoz.

Ha az MFA-főkiszolgáló offline állapotba kerül, a hitelesítések továbbra is feldolgozhatók, de az MFA-adatbázis módosítását igénylő műveletek nem dolgozhatók fel. (Ilyenek például a felhasználók hozzáadása, az önkiszolgáló PIN-kód módosítása, a felhasználói adatok módosítása vagy a felhasználói portálhoz való hozzáférés)

## <a name="deployment"></a>Környezet

Vegye figyelembe a következő fontos pontokat az Azure MFA Server és a kapcsolódó összetevők terheléselosztáshoz.

* **Radius-szabvány használata a magas rendelkezésre állás eléréséhez.** Ha az Azure MFA-kiszolgálókat RADIUS-kiszolgálóként használja, potenciálisan konfigurálhat egy MFA-kiszolgálót elsődleges RADIUS-hitelesítési célként, és más Azure MFA-kiszolgálókat másodlagos hitelesítési tárolóként. Ez a módszer azonban a magas rendelkezésre állás elérése érdekében nem lehet praktikus, mert meg kell várni a time-out időszak bekövetkezése, amikor a hitelesítés sikertelen az elsődleges hitelesítési cél, mielőtt hitelesíthető a másodlagos hitelesítési cél. Hatékonyabb a RADIUS-ügyfél és a RADIUS-kiszolgálók (ebben az esetben az Azure MFA-kiszolgálók RADIUS-kiszolgálókként működő) közötti RADIUS-forgalom elosztása, így a RADIUS-ügyfeleket egyetlen URL-címmel konfigurálhatja, amelyre rámutathatnak.
* **Az MFA-beosztottak manuális előléptetése**szükséges. Ha a fő Azure MFA-kiszolgáló offline állapotba kerül, a másodlagos Azure MFA-kiszolgálók továbbra is feldolgozzák az MFA-kérelmeket. Azonban, amíg a fő MFA-kiszolgáló áll rendelkezésre, a rendszergazdák nem adhat nak hozzá felhasználókat, vagy módosíthatják az MFA beállításait, és a felhasználók nem módosíthatják a felhasználói portálon. A főszerepköralá rendelt Többszintű hitelesítés támogatása mindig manuális folyamat.
* **Az összetevők elkülöníthetősége**. Az Azure MFA-kiszolgáló több összetevőből áll, amelyek telepíthetők ugyanarra a Windows Server-példányra vagy különböző példányokra. Ezek közé az összetevők közé tartozik a felhasználói portál, a mobilalkalmazás-webszolgáltatás és az ADFS-adapter (ügynök). Ez az elválasztódás lehetővé teszi a webalkalmazás-proxy használatát a felhasználói portál és a mobilalkalmazás webkiszolgálójának a peremhálózatról való közzétételére. Az ilyen konfiguráció növeli a terv általános biztonságát, ahogy az az alábbi ábrán látható. Az MFA felhasználói portál és a mobilalkalmazás-webkiszolgáló ha-terheléselosztásos konfigurációkban is telepíthető.

   ![MFA-kiszolgáló peremhálózattal](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Egyszeri jelszó (OTP) SMS-ben (más néven egyirányú SMS) használatához ragadós ülés, ha a forgalom teher-kiegyensúlyozott**. Az egyirányú SMS egy hitelesítési lehetőség, amelynek hatására az MFA-kiszolgáló otp-t tartalmazó szöveges üzenetet küld a felhasználóknak. A felhasználó egy gyors ablakban adja meg az OTP-t az MFA-kihívás teljesítéséhez. Ha az Azure MFA-kiszolgálók at tölti be, ugyanaz a kiszolgáló, amely az eredeti hitelesítési kérelmet kézbesíti, annak a kiszolgálónak kell lennie, amely megkapja az OTP-üzenetet a felhasználótól; ha egy másik MFA-kiszolgáló megkapja az OTP-választ, a hitelesítési kihívás sikertelen lesz. További információ: [One Time Password over SMS Added to Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **A felhasználói portál és a mobilalkalmazás-webszolgáltatás terheléselosztásos telepítései ragadós munkameneteket igényelnek.** Ha az MFA felhasználói portálját és a mobilalkalmazás-webszolgáltatást tölti el, minden munkamenetnek ugyanazon a kiszolgálón kell maradnia.

## <a name="high-availability-deployment"></a>Magas rendelkezésre állású telepítés

Az alábbi ábrán az Azure MFA és összetevői teljes HA terheléselosztásos implementációja, valamint az ADFS referencia.

 ![Az Azure MFA Server HA implementációja](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Vegye figyelembe a következő elemeket az előző ábra megfelelő számozott területéhez.

1. A két Azure MFA-kiszolgáló (MFA1 és MFA2) terheléselosztású (mfaapp.contoso.com), és statikus port (4443) használatára vannak konfigurálva a PhoneFactor.pfdata adatbázis replikálásához. A webszolgáltatás SDK-ja minden MFA-kiszolgálón telepítve van, hogy lehetővé tegye a 443-as TCP-porton keresztüli kommunikációt az ADFS-kiszolgálókkal. Az MFA-kiszolgálók állapotnélküli terheléselosztásos konfigurációban vannak telepítve. Ha azonban AZ OTP-t SMS-ben szeretné használni, állapotalapú terheléselosztást kell használnia.
   ![Azure MFA-kiszolgáló – Alkalmazáskiszolgáló HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Mivel az RPC dinamikus portokat használ, nem ajánlott a tűzfalakat megnyitni az RPC által potenciálisan használható dinamikus portok tartományában. Ha tűzfal van az MFA-alkalmazáskiszolgálók **között,** konfigurálnia kell az MFA-kiszolgálót úgy, hogy statikus porton kommunikáljon az alárendelt és a főkiszolgálókiszolgálók közötti replikációs forgalomról, és nyissa meg a portot a tűzfalon. A statikus portot úgy kényszerítheti, hogy ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` ```Pfsvc_ncan_ip_tcp_port``` létrehoz egy duplaszó rendszerleíró értéket a hívott helyen, és az értéket egy elérhető statikus portra állítja. A kapcsolatokat mindig az alárendelt MFA-kiszolgálók kezdeményezik a főkiszolgálóval, a statikus port csak a főkiszolgálón szükséges, de mivel bármikor előléptethet egy alárendeltt főkiszolgálóvá, a statikus portot az összes MFA-kiszolgálón be kell állítani.

2. A két felhasználói portál/MFA mobilalkalmazás-kiszolgáló (MFA-UP-MAS1 és MFA-UP-MAS2) **terheléselosztásos állapotalapú** konfigurációban (mfa.contoso.com). Emlékezzünk vissza, hogy a ragadós munkamenetek az MFA felhasználói portál és a mobilalkalmazás-szolgáltatás terheléselosztásának követelménye.
   ![Azure MFA-kiszolgáló – Felhasználói portál és mobilalkalmazás-szolgáltatás HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Az ADFS-kiszolgálófarm terheléselosztással van elhatárolt, és a peremhálózaton lévő terheléselosztásos ADFS-proxykon keresztül kerül az internetre. Minden ADFS-kiszolgáló az ADFS-ügynök segítségével kommunikál az Azure MFA-kiszolgálókkal egyetlen terheléselosztással ellátott URL-cím (mfaapp.contoso.com) használatával a 443-as TCP-porton keresztül.

## <a name="next-steps"></a>További lépések

* [Az Azure MFA Server telepítése és konfigurálása](howto-mfaserver-deploy.md)
