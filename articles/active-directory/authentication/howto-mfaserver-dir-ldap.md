---
title: LDAP-hitelesítés és Azure MFA-kiszolgáló – az Azure Active Directory
description: LDAP-hitelesítés és az Azure multi-factor Authentication-kiszolgáló üzembe helyezése.
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
ms.openlocfilehash: 78879f0e54dbd8f573ade7381ba83429346d00db
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314263"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-hitelesítés és Azure multi-factor Authentication-kiszolgáló

Alapértelmezés szerint az Azure Multi-Factor Authentication-kiszolgáló úgy van konfigurálva, hogy a felhasználókat az Active Directoryból importálja vagy szinkronizálja. Azonban konfigurálható úgy különböző LDAP-címtárakhoz való kötésre is, például ADAM-címtárhoz vagy adott Active Directory-tartományvezérlőkhöz. Ha egy címtárhoz LDAP-n keresztül csatlakozik, az Azure multi-factor Authentication-kiszolgáló működhet-e LDAP-proxyként működjön a hitelesítések végrehajtásához. Ezenkívül lehetővé teszi, hogy az LDAP-kötést felhasználja RADIUS-célként az IIS-hitelesítést használó felhasználók előhitelesítésére illetve elsődleges hitelesítésre az Azure MFA felhasználói portálon.

Használja az Azure multi-factor Authentication LDAP-proxyként, helyezze be az Azure multi-factor Authentication kiszolgáló az LDAP-ügyfél (például VPN-készülék, alkalmazás) és az LDAP-címtárkiszolgáló között. Az Azure Multi-Factor Authentication-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és az LDAP-címtárral is kommunikáljon. Ebben a konfigurációban az Azure Multi-Factor Authentication-kiszolgáló fogadja az ügyfélkiszolgálóktól és alkalmazásoktól érkező LDAP-kéréseket, majd továbbítja őket a cél LDAP-címtárkiszolgálónak az elsődleges hitelesítő adatok ellenőrzéséhez. Az LDAP-címtár érvényesíti az elsődleges hitelesítő adatokat, ha az Azure multi-factor Authentication második személyazonosság-ellenőrzést hajt végre, és visszaküldi a választ az LDAP-ügyfél. A teljes hitelesítés csak akkor lesz sikeres, ha az LDAP-kiszolgálón való hitelesítés és a kétlépéses hitelesítés is sikeres.

## <a name="configure-ldap-authentication"></a>LDAP-hitelesítés konfigurálása
Az LDAP-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálón. Kövesse az alábbi eljárást:

### <a name="add-an-ldap-client"></a>LDAP-ügyfél hozzáadása

1. Az Azure multi-factor Authentication-kiszolgálón válassza a bal oldali menüben lévő LDAP-hitelesítés ikonra.
2. Jelölje be az **LDAP-hitelesítés engedélyezése** jelölőnégyzetet.

   ![LDAP-hitelesítés](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Az Ügyfelek lapon módosítsa a TCP-portot és az SSL-portot, ha az Azure Multi-Factor Authentication LDAP-szolgáltatását nem szabványos portokhoz szeretné kötni, hogy fogadni tudjon LDAP-kéréseket.
4. Ha azt tervezi, LDAPS-t használja az ügyfél az Azure multi-factor Authentication-kiszolgáló, egy SSL-tanúsítványt kell az MFA-kiszolgálóval megegyező kiszolgálóra telepíthető. Kattintson a **Tallózás** mellett az SSL-tanúsítvány mezőbe, és válasszon ki egy tanúsítványt a biztonságos kapcsolathoz való használatra.
5. Kattintson a **Hozzáadás** parancsra.
6. Az LDAP-ügyfél hozzáadása párbeszédpanelen adja meg az IP-címét a készülék, kiszolgáló vagy alkalmazás, amely hitelesíti a kiszolgáló és a egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
7. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó nem még lettek importálva a kiszolgálóra és/vagy a kétlépéses ellenőrzés alól, hagyja bejelölve a mezőt. Tekintse meg az MFA-kiszolgáló súgófájl a funkció további információt.

További LDAP-ügyfelek hozzáadásához ismételje meg ezeket a lépéseket.

### <a name="configure-the-ldap-directory-connection"></a>Az LDAP-címtár kapcsolatának konfigurálása

Ha az Azure Multi-Factor Authentication LDAP-hitelesítések fogadására van konfigurálva, a hitelesítéseket az LDAP-címtárnak kell proxykapcsolaton keresztül átadnia. Ezért a Cél lapon csak egyetlen, szürkén megjelenő lehetőség látható LDAP-cél használatához.

1. Az LDAP-címtár kapcsolatának konfigurálásához kattintson a **Címtár-integráció** ikonra.
2. A Beállítások lapon jelölje be a **Megadott LDAP-konfiguráció használata** választógombot.
3. Válassza a **Szerkesztés…** elemet.
4. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az LDAP-címtárhoz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az Azure Multi-Factor Authentication-kiszolgáló súgófájljában találja.

    ![Címtár-integráció](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Tesztelje az LDAP-kapcsolatot a **Tesztelés** gombra kattintva.
6. Ha az LDAP-kapcsolat tesztelése sikeres volt, kattintson az **OK** gombra.
7. Kattintson a **Szűrők** fülre. A kiszolgáló előre konfigurálva van a tárolók, biztonsági csoportok és felhasználók az Active Directoryból való betöltésére. Másik LDAP-címtárhoz való kötés esetén valószínűleg szerkesztenie kell a megjelenített szűrőket. A szűrőkkel kapcsolatos további információért kattintson a **Súgó** hivatkozásra.
8. Kattintson az **Attribútumok** fülre. A kiszolgáló előre konfigurálva van az Active Directoryból származó attribútumok leképezésére.
9. Másik LDAP-címtárhoz való kötéshez vagy az előre konfigurált attribútumleképezések módosításához kattintson a **Szerkesztés…** elemre
10. Az Attribútumok szerkesztése párbeszédpanelen módosítsa az LDAP-attribútumleképezéseket a címtárra vonatkozóan. Az attribútumneveket beírhatja, vagy kiválaszthatja a mezők melletti **…** gombra kattintva. Az attribútumokkal kapcsolatos további információért kattintson a **Súgó** hivatkozásra.
11. Kattintson az **OK** gombra.
12. Kattintson a **Vállalati beállítások** ikonra, és válassza a **Felhasználónév feloldása** fület.
13. Ha az Active Directoryhoz egy tartományhoz csatlakoztatott kiszolgálóról csatlakozik, hagyja bejelölve a **Windows biztonsági azonosítók (SID-k) használata a felhasználónevek egyeztetéséhez** választógombot. Egyéb esetben jelölje be az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** választógombot. 

Ha az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** lehetőséget bejelöli, az Azure Multi-Factor Authentication-kiszolgáló a felhasználóneveket megkísérli egy egyedi azonosítóvá feloldani az LDAP-címtárban. A rendszer végrehajt egy LDAP-keresést a Címtár-integráció -> Attribútumok lapon meghatározott felhasználónév-attribútumokon. Amikor egy felhasználó hitelesíti magát, a felhasználónév megoldódott-e a egyedi azonosítóval, az LDAP-címtárban. Az egyedi azonosító a felhasználónak az Azure multi-factor Authentication adatfájlban szolgál. Ez lehetővé teszi a kis-és összehasonlításáért és a hosszú és rövid felhasználónév-formátumok használatát.

Miután végrehajtotta ezeket a lépéseket, az MFA-kiszolgáló a konfigurált ügyfelek LDAP-hozzáférési kéréseit a beállított portokon figyel, és proxyként működik a kéréseket proxykapcsolaton az LDAP-címtárnak hitelesítésre.

## <a name="configure-ldap-client"></a>LDAP-ügyfél konfigurálása
Az LDAP-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a készüléket, kiszolgálót vagy alkalmazást, hogy úgy végezzen hitelesítést LDAP-n keresztül az Azure Multi-Factor Authentication-kiszolgálón, mintha az az Ön LDAP-címtára lenne. Használja ugyanazokat a beállításokat, amelyeket általában használna az LDAP-címtárához való közvetlen csatlakozáshoz, azonban az Azure Multi-Factor Authentication-kiszolgáló kiszolgálónevét vagy IP-címét adja meg.
* A LDAP időtúllépést állítsa 30 – 60 másodpercre, hogy nincs ideje az LDAP-címtárhoz a felhasználó hitelesítő adatainak ellenőrzésére, hajtsa végre a kétlépéses, a válasz fogadására és az LDAP-hozzáférési kérés megválaszolására.
* Ha LDAPS-t használ, az LDAP-lekérdezéseket kezdeményező készüléknek vagy kiszolgálónak meg kell bíznia az Azure Multi-Factor Authentication-kiszolgálón telepített SSL-tanúsítványban.

