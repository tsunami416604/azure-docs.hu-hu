---
title: "Magas rendelkezésre állásra konfigurálja a Azure MFA kiszolgáló |} Microsoft Docs"
description: "Azure multi-factor Authentication kiszolgáló magas rendelkezésre állást biztosító konfigurációk több példányának telepítése."
services: multi-factor-authentication
keywords: Az Azure MFA
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: f22e0df75830a048e535384de8a3eec51bf91fd8
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Magas rendelkezésre állásra konfigurálja a Azure multi-factor Authentication kiszolgáló

Magas rendelkezésre állású telepítés Azure MFA-kiszolgáló eléréséhez több multi-factor Authentication kiszolgálót telepíteni kell. Ez a szakasz tájékoztatást nyújt egy elosztott terhelésű tervezési Azure MFS Server-telepítéséhez, a magas rendelkezésre állású célok eléréséhez.

## <a name="mfa-server-overview"></a>Multi-factor Authentication kiszolgáló – áttekintés

Az Azure MFA kiszolgáló szolgáltatás architektúrája több összetevőből áll, az alábbi ábrán látható módon:

 ![Multi-factor Authentication kiszolgáló architektúrája](./media/mfa-server-high-availability/mfa-ha-architecture.png)

Az MFA kiszolgáló az Azure multi-factor Authentication szoftverekkel rendelkező Windows kiszolgáló. Működéséhez a multi-factor Authentication szolgáltatást az Azure-ban aktiválnia kell a multi-factor Authentication Server-példány. Egynél több multi-factor Authentication kiszolgáló lehet a telepített helyszíni.

Az első MFA-kiszolgáló, amely telepítve a fő multi-factor Authentication kiszolgáló alapértelmezés szerint az Azure MFA szolgáltatás aktiválása után. A fő multi-factor Authentication kiszolgáló van a PhoneFactor.pfdata adatbázis írható másolata. Multi-factor Authentication Server-példányok telepítéseinél slaves nevezik. A többtényezős hitelesítés slaves van a PhoneFactor.pfdata adatbázis replikált csak olvasható másolata. Multi-factor Authentication kiszolgáló replikálja a távoli eljáráshívás (RPC) használatával információkat. Minden multi-factor Authentication kiszolgálóknak együttesen kell tartományhoz csatlakoztatott vagy önálló replikálja az adatokat.

Többtényezős hitelesítés fő és az alárendelt multi-factor Authentication kiszolgálók kommunikálni a multi-factor Authentication szolgáltatás Ha kétfaktoros hitelesítést szükség. Például amikor egy felhasználó megpróbál hozzáférni egy kéttényezős hitelesítést igénylő alkalmazás, a felhasználó először hitelesítése az identitásszolgáltatótól, például az Active Directory (AD).

Az ad-vel a sikeres hitelesítés után az MFA kiszolgáló a multi-factor Authentication szolgáltatással kommunikál. A multi-factor Authentication kiszolgáló megvárja-e a notification engedélyezi vagy elutasítja a felhasználó hozzáférni az alkalmazáshoz a multi-factor Authentication szolgáltatásból.

A fő multi-factor Authentication kiszolgáló offline állapotba kerül, ha hitelesítési események feldolgozása még lehet, de nem dolgozható fel a multi-factor Authentication adatbázis módosításait igénylő műveletekhez. (Például: a felhasználók, önkiszolgáló PIN módosítása, és felhasználói adatok módosítása)

## <a name="deployment"></a>Környezet

Vegye figyelembe a következő fontos szempontokat az Azure MFA kiszolgáló és a kapcsolódó összetevők terheléselosztási.

* **A RADIUS szabványos magas rendelkezésre állás**. Ha az Azure MFA kiszolgáló RADIUS-kiszolgálóként használ, potenciálisan konfigurálhatja egy multi-factor Authentication kiszolgáló elsődleges RADIUS-hitelesítés célként, és a többi Azure MFA kiszolgáló másodlagos hitelesítés célként. Azonban ez a módszer a magas rendelkezésre állás nem lehet, hogy gyakorlati meg kell várnia a hitelesítés meg az elsődleges hitelesítés nem sikerül, akkor is hitelesíthetők a másodlagos hitelesítés célon előtt történjen időtúllépés. A RADIUS-forgalom a RADIUS-ügyfél és a RADIUS-kiszolgálók (ebben az esetben, az Azure MFA kiszolgáló RADIUS-kiszolgálóként működő) között hatékonyabb, hogy a RADIUS-ügyfelek konfigurálhatja azokat mutathat egy URL-címet.
* **Manuálisan főkiszolgálóvá előléptetni MFA slaves kell**. A fő Azure MFA kiszolgáló offline állapotba kerül, ha a másodlagos Azure MFA kiszolgáló folytatja a többtényezős hitelesítési kérelmeket. Azonban addig, amíg a fő multi-factor Authentication kiszolgáló nem érhető el, rendszergazdák nem adja hozzá a felhasználókat vagy többtényezős hitelesítési beállítások módosítása, és felhasználók nem módosíthatja a felhasználói portál használatával. Az MFA alárendelt a főkiszolgáló szerepkörének előléptetni mindig egy kézi művelet.
* **Az összetevők separability**. Az Azure MFA kiszolgáló telepíthető a Windows Server-példányt, vagy különböző példányok több összetevőt magában foglalja. Ezen összetevők közé tartozik a felhasználói portál, a mobilalkalmazás webszolgáltatás és az AD FS-adapter (ügynök). A separability lehetővé teszi a webalkalmazás-Proxy használata a felhasználói portál és Mobile App webkiszolgáló közzétételéhez a szegélyhálózaton. Ilyen konfiguráció hozzáadása a tervezés, általános biztonságosságát, az alábbi ábrán látható módon. A multi-factor Authentication felhasználói portál és Mobile App webkiszolgáló is telepítésére a magas rendelkezésre ÁLLÁSÚ terhelésű konfigurációkban.

   ![MFA kiszolgáló a szegélyhálózaton](./media/mfa-server-high-availability/mfasecurity.png)

* **Egyszeri jelszavas (OTP) SMS (más néven egyirányú SMS) keresztül kapcsolódó munkamenetek használatát igényli, ha forgalom terhelésű**. Egyirányú SMS egy, a multi-factor Authentication kiszolgáló a felhasználók küldeni szöveges üzenetben egy egyszeri Jelszavas hitelesítési lehetőséget. A felhasználó megadja az egyszeri jelszó kérése ablakban az MFA-kérdést befejezéséhez. Végezze el Azure MFA kiszolgáló, ha a kezdeti hitelesítési kérés ugyanarra a kiszolgálóra kell a kiszolgálót, amely az egyszeri Jelszavas üzenetet kap a felhasználó; Ha egy másik multi-factor Authentication kiszolgáló az egyszeri Jelszavas választ kap, a hitelesítési kérdés sikertelen lesz. További információkért lásd: [egy idő jelszavát SMS hozzáadott Azure MFA kiszolgáló](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **A felhasználói portál és a mobilalkalmazás webszolgáltatása terhelésű telepítés igényel a kapcsolódó munkamenetek**. Ha meg vannak terheléselosztás a multi-factor Authentication felhasználói portál és a mobilalkalmazás webszolgáltatása, mindegyik munkamenethez kell ugyanazon a kiszolgálón maradnak.

## <a name="high-availability-deployment"></a>Magas rendelkezésre állású telepítés

Az alábbi ábrán egy teljes magas rendelkezésre ÁLLÁSÚ terhelésű végrehajtása az Azure MFA és összetevőit, és az AD FS referenciaként.

 ![Az Azure MFA kiszolgáló magas rendelkezésre ÁLLÁSÚ végrehajtása](./media/mfa-server-high-availability/mfa-ha-deployment.png)

Vegye figyelembe a következő elemek a fenti ábrán használat számozott területre.

1. A két Azure MFA-kiszolgáló (MFA1 és MFA2) elosztott terhelésű (mfaapp.contoso.com) és egy statikus port használatára van konfigurálva (4443) a PhoneFactor.pfdata adatbázis replikálásához. A webszolgáltatási SDK telepítve van a multi-factor Authentication kiszolgáló kommunikáció engedélyezése az AD FS-kiszolgáló a 443-as TCP-porton keresztül. A multi-factor Authentication kiszolgáló állapot nélküli elosztott terhelésű konfigurációban lett telepítve. Azonban ha OTP SMS keresztül használja, kell használnia az állapotalapú terheléselosztás.
   ![Az Azure MFA kiszolgáló - alkalmazások kiszolgálói magas rendelkezésre ÁLLÁSÚ](./media/mfa-server-high-availability/mfaapp.png)

   > [!NOTE]
   > Mivel az RPC dinamikus portok használja, nem ajánlott tűzfalak, akár a körét, amelyek segítségével RPC dinamikus portok megnyitásához. Ha a tűzfal **közötti** az MFA alkalmazáskiszolgálók, konfigurálnia kell a multi-factor Authentication kiszolgáló egy statikus port a replikációs forgalom az alárendelt és fő kiszolgálók közötti kommunikációhoz, és nyissa meg ezt a portot a tűzfalon. Hozzon létre egy DWORD beállításazonosítót, kényszerítheti a statikus port ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` nevű ```Pfsvc_ncan_ip_tcp_port``` és a rendelkezésre álló statikus portra értékre állítja. Kapcsolatok mindig a fő az alárendelt multi-factor Authentication kiszolgálók által kezdeményezett, a statikus port csak szükséges fő, de mivel előléptetheti kell lennie a fő bármikor főkiszolgálójával kezd, célszerű minden multi-factor Authentication kiszolgálón a statikus port.

2. A két felhasználói portál/multi-factor Authentication mobilalkalmazás-kiszolgálók (MFA-felfelé-MAS1 és MFA-felfelé-MAS2) esetén az elosztott terhelésű egy **állapotalapú alkalmazások és szolgáltatások** konfigurációs (mfa.contoso.com). A visszaírási, hogy-e a kapcsolódó munkamenetek előfeltétele annak, hogy a multi-factor Authentication felhasználói portál és az Mobile App Service terheléselosztási.
   ![Az Azure MFA kiszolgáló - a felhasználói portál és Mobile App szolgáltatás magas rendelkezésre ÁLLÁSÚ](./media/mfa-server-high-availability/mfaportal.png)
3. Az AD FS kiszolgálófarm terhelése eloszlik és közzétéve az interneten keresztül terheléselosztással rendelkező AD FS proxyk peremhálózatban. Minden ADFS-kiszolgáló az AD FS-ügynök használatával kommunikálni az Azure multi-factor Authentication kiszolgálók egy elosztott terhelésű URL-címet (mfaapp.contoso.com) használatával a 443-as TCP-porton keresztül.

## <a name="next-steps"></a>Következő lépések

* [Azure MFA kiszolgáló telepítése és konfigurálása](multi-factor-authentication-get-started-server.md)
