---
title: Azure MFA-kiszolgáló konfigurálása a magas rendelkezésre álláshoz |} A Microsoft Docs
description: Az Azure multi-factor Authentication kiszolgáló konfigurációban, magas rendelkezésre állást biztosító több példányának telepítése.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26216131b461a395d3c87895a055d3ae30abf87c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198813"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Azure multi-factor Authentication-kiszolgáló magas rendelkezésre állás konfigurálása

Magas rendelkezésre állást biztosít az Azure MFA-kiszolgáló telepítéssel eléréséhez szüksége több MFA-kiszolgáló telepítése. Ez a szakasz az Azure MFS Server-telepítéséhez, a magas rendelkezésre állású célok eléréséhez egy elosztott terhelésű tervezési információkat biztosít.

## <a name="mfa-server-overview"></a>MFA-kiszolgáló – áttekintés

Az Azure MFA-kiszolgáló szolgáltatás architektúrája több összetevőből áll, az alábbi ábrán látható módon:

 ![MFA-kiszolgáló architektúrája](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Az MFA-kiszolgáló az az Azure multi-factor Authentication szoftverrel rendelkező Windows Server. Az MFA-kiszolgálópéldányra aktiválnia kell az Azure MFA szolgáltatás működéséhez. Egynél több MFA-kiszolgáló lehet a telepített helyszíni.

Az első MFA-kiszolgáló, amely telepítve a fő MFA-kiszolgáló által alapértelmezés szerint az Azure MFA szolgáltatás aktiválása után. A fő MFA-kiszolgáló rendelkezik a PhoneFactor.pfdata adatbázis írható másolatát. További példányok MFA-kiszolgáló telepítésekor a beosztottak nevezzük. A többtényezős hitelesítés beosztottak replikált csak olvasható adatbázis egy példányát a PhoneFactor.pfdata rendelkezik. MFA-kiszolgálók replikálása a távoli eljáráshívás (RPC) használatával. Többtényezős hitelesítés minden kiszolgálója együttesen kell kell tartományhoz csatlakoztatott vagy önálló replikálja az adatokat.

MFA fő és a többtényezős hitelesítés az alárendelt kiszolgálók kommunikálnak az MFA szolgáltatással, ha kétfaktoros hitelesítés szükséges. Például amikor egy felhasználó megpróbál kéttényezős hitelesítést igénylő alkalmazás eléréséhez, a felhasználó először hitelesítése egy identitásszolgáltató, például az Active Directory (AD).

Az ad-vel a sikeres hitelesítés után az MFA-kiszolgáló az MFA szolgáltatással kommunikál. Az MFA-kiszolgáló megvárja, amíg az MFA szolgáltatás engedélyezéséhez vagy letiltásához az alkalmazás eléréséhez a felhasználó értesítést.

A fő MFA-kiszolgáló offline állapotba kerül, ha hitelesítések továbbra is választhatók, de a módosítások szükségesek, hogy az MFA-adatbázis-műveletek nem dolgozható fel. (Példák: a felhasználók, önkiszolgáló PIN-kód módosításokat, és felhasználói adatok módosítása)

## <a name="deployment"></a>Környezet

Vegye figyelembe az alábbi fontos szempontokat terheléselosztási Azure MFA-kiszolgáló és kapcsolódó összetevői.

* **RADIUS-szabvány használatával magas rendelkezésre állás**. Ha az Azure MFA-kiszolgálót RADIUS-kiszolgálóként használ, potenciálisan konfigurálhatja egy MFA-kiszolgáló elsődleges RADIUS-hitelesítés célként és más Azure MFA-kiszolgálókat a másodlagos hitelesítés célként. Azonban ez a módszer a magas rendelkezésre állás nem lehet gyakorlati mert időtúllépés fordulhat elő, ha a hitelesítés sikertelen az elsődleges hitelesítési célon, akkor is hitelesíthetők a másodlagos hitelesítési célon előtt meg kell várnia. Terheléselosztás a RADIUS-forgalmat a RADIUS-ügyfél és a RADIUS-kiszolgálók (az ebben az esetben az Azure MFA-kiszolgálók a RADIUS-kiszolgálóként működő) között hatékonyabb úgy, hogy konfigurálhatja a RADIUS-ügyfelektől, mutathat egyetlen URL-címet.
* **Manuálisan főkiszolgálóvá előléptetni a többtényezős hitelesítés a beosztottak kell**. A fő Azure MFA-kiszolgáló offline állapotba kerül, ha az Azure MFA másodlagos kiszolgálók továbbra is az MFA-kérelmeket. Azonban amíg nem érhető el a fő MFA-kiszolgáló, a rendszergazdák nem felhasználók hozzáadása vagy módosítása az MFA-beállítások, és felhasználók nem módosíthatja a felhasználói portál használatával. A többtényezős hitelesítés támogatása a főkiszolgálói szerepkör alárendelt, mindig manuális folyamat.
* **Összetevők separability**. Az Azure MFA-kiszolgáló, amely telepíthető ugyanazon a Windows Server-példányon, vagy a különböző példányokon több összetevőt magában foglalja. Ezen összetevők közé tartoznak a felhasználói portálra, a Mobile App Web Service és az ADFS-adaptert (ügynök). Ez separability lehetővé teszi a felhasználói portál és a Mobile App Web Server közzététele a szegélyhálózatról a webalkalmazás-Proxy használatával. Ilyen konfiguráció hozzáadása a tervezés, az általános biztonságának, az alábbi ábrán látható módon. Az MFA felhasználói portál és a Mobile App Web Server is is üzembe helyezhetők a magas rendelkezésre ÁLLÁSÚ, elosztott terhelésű konfigurációkban.

   ![MFA-kiszolgáló a szegélyhálózaton](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **SMS (más néven az egyirányú SMS) keresztül egyszeri jelszavas (OTP) fix kiszolgálású munkameneteket használatát igényli, elosztott terhelésű forgalom esetén**. Egyirányú SMS, amely hatására az OTP tartalmazó szöveges üzenetet küldjön a felhasználóknak az MFA-kiszolgáló hitelesítési lehetőség. A felhasználó ad vissza kell Küldenie egy kérdés ablakban végezze el az MFA-hitelesítést. Ha Ön terhelésének elosztásához az Azure MFA-kiszolgálóval, ugyanarra a kiszolgálóra, amely a kezdeti hitelesítési kérés kell lennie a kiszolgálón, amely az egyszeri Jelszavas üzenetet kap a felhasználó; Ha egy másik MFA-kiszolgáló az OTP-válasz érkezik, a hitelesítési kérdés sikertelen lesz. További információkért lásd: [keresztül SMS hozzá az Azure MFA-kiszolgáló egy idő jelszó](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Elosztott terhelésű példányainak a felhasználói portál és a Mobile App Web Service megkövetelése fix kiszolgálású munkameneteket**. Ha meg vannak terheléselosztási az MFA felhasználói portál és a Mobile App Web Service, minden egyes munkamenetnél kell ugyanazon a kiszolgálón maradnak.

## <a name="high-availability-deployment"></a>Magas rendelkezésre állású üzembe helyezés

Az alábbi ábrán látható egy teljes magas rendelkezésre ÁLLÁSÚ elosztott terhelésű megvalósítását az Azure MFA és az összetevőinek, ADFS referenciaként együtt.

 ![Az Azure MFA Server magas rendelkezésre ÁLLÁS végrehajtása](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Vegye figyelembe a következő elemeket a fentebbi ábra ennek megfelelően a rendszer számokból álló területre.

1. A két Azure MFA-kiszolgáló (MFA1 és MFA2) terheléselosztással oszlanak meg (mfaapp.contoso.com) és a egy statikus port használatára van konfigurálva (4443) a PhoneFactor.pfdata replikálhatók. A Web Service SDK telepítve van az MFA-kiszolgáló engedélyezheti a kommunikációt az ADFS-kiszolgálók a 443-as TCP-porton keresztül. Az MFA-kiszolgálók állapot nélküli elosztott terhelésű konfigurációban vannak telepítve. Azonban ha használja az OTP SMS-en keresztül, kell használnia állapot-nyilvántartó terheléselosztást.
   ![Az Azure MFA-kiszolgáló - alkalmazáskiszolgáló magas rendelkezésre ÁLLÁS](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Mivel az RPC dinamikus portok használja, nem ajánlott tűzfalak, akár a körét, amely potenciálisan használható RPC dinamikus portok megnyitásához. Ha van egy tűzfal **között** az MFA az alkalmazáskiszolgálókat, konfigurálnia kell az MFA-kiszolgáló kommunikálhasson a fő- és alárendelt kiszolgálók közötti replikációs forgalom statikus portot, és nyissa meg az erre a portra a tűzfalon. Hozzon létre egy DWORD beállításazonosítót, kényszerítheti a statikus port ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` nevű ```Pfsvc_ncan_ip_tcp_port``` és a egy rendelkezésre álló statikus portra értékre állítja. Kapcsolatok mindig a fő az alárendelt MFA-kiszolgálók által kezdeményezett, a statikus portot csak a fő szükséges, de bármikor a fő kell egy alárendelt léptetheti elő, mivel minden MFA-kiszolgálón kell beállítania a statikus portot.

2. A két felhasználói portál/MFA Mobile Apps-kiszolgálók (MFA-UP-MAS1 és az MFA-UP-MAS2) az elosztott terhelésű egy **állapotalapú** configuration (mfa.contoso.com). Ne felejtse el, hogy fix kiszolgálású munkameneteket-e az MFA felhasználói portál és a Mobile App Service terheléselosztási követelmény.
   ![Az Azure MFA-kiszolgáló – felhasználói portál és a Mobile App szolgáltatás magas rendelkezésre ÁLLÁS](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Az AD FS-kiszolgálófarm terhelése eloszlik és közzétéve az interneten keresztül az AD FS-proxy elosztott terhelésű a szegélyhálózaton. Minden ADFS-kiszolgáló az AD FS-ügynök a 443-as TCP-porton keresztül egy elosztott terhelésű URL-címet (mfaapp.contoso.com) használatával az Azure MFA-kiszolgálóval folytatott kommunikációhoz használ.

## <a name="next-steps"></a>További lépések

* [Telepítse és konfigurálja az Azure MFA-kiszolgáló](howto-mfaserver-deploy.md)
