---
title: LDAP-hitelesítés és Azure MFA-kiszolgáló | Microsoft Docs
description: Ez az Azure Multi-Factor Authentication-oldal segítséget nyújt az LDAP-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló telepítéséhez.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.openlocfilehash: 37f96afab67680b3140a6f4da6bff0c23a1f328f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
ms.locfileid: "31614476"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-hitelesítés és az Azure multi-factor Authentication kiszolgáló
Alapértelmezés szerint az Azure Multi-Factor Authentication-kiszolgáló úgy van konfigurálva, hogy a felhasználókat az Active Directoryból importálja vagy szinkronizálja. Azonban konfigurálható úgy különböző LDAP-címtárakhoz való kötésre is, például ADAM-címtárhoz vagy adott Active Directory-tartományvezérlőkhöz. LDAP keresztül könyvtár való csatlakozáskor az Azure multi-factor Authentication kiszolgáló a hitelesítés végrehajtásához LDAP-proxyként működhet. Ezenkívül lehetővé teszi, hogy az LDAP-kötést felhasználja RADIUS-célként az IIS-hitelesítést használó felhasználók előhitelesítésére illetve elsődleges hitelesítésre az Azure MFA felhasználói portálon.

Használja az Azure multi-factor Authentication LDAP-proxyként, helyezze az Azure multi-factor Authentication kiszolgáló között az LDAP-ügyfél (például a VPN-készülék, az alkalmazás) és az LDAP címtárkiszolgálón. Az Azure Multi-Factor Authentication-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és az LDAP-címtárral is kommunikáljon. Ebben a konfigurációban az Azure Multi-Factor Authentication-kiszolgáló fogadja az ügyfélkiszolgálóktól és alkalmazásoktól érkező LDAP-kéréseket, majd továbbítja őket a cél LDAP-címtárkiszolgálónak az elsődleges hitelesítő adatok ellenőrzéséhez. Ha az LDAP-címtár ellenőrzi az elsődleges hitelesítő adatait, Azure multi-factor Authentication második azonosító ellenőrzi és elküld egy választ, az LDAP-ügyfélnek. A teljes hitelesítés csak akkor lesz sikeres, ha az LDAP-kiszolgálón való hitelesítés és a kétlépéses hitelesítés is sikeres.

## <a name="configure-ldap-authentication"></a>LDAP-hitelesítés konfigurálása
Az LDAP-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálón. Kövesse az alábbi eljárást:

### <a name="add-an-ldap-client"></a>LDAP-ügyfél hozzáadása

1. Válassza ki az Azure multi-factor Authentication kiszolgáló az LDAP-hitelesítés ikonra a bal oldali menüben.
2. Jelölje be az **LDAP-hitelesítés engedélyezése** jelölőnégyzetet.

   ![LDAP-hitelesítés](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. Az Ügyfelek lapon módosítsa a TCP-portot és az SSL-portot, ha az Azure Multi-Factor Authentication LDAP-szolgáltatását nem szabványos portokhoz szeretné kötni, hogy fogadni tudjon LDAP-kéréseket.
4. Ha azt tervezi, az ügyfél LDAPS segítségével az Azure multi-factor Authentication kiszolgáló, az SSL-tanúsítvány MFA kiszolgáló ugyanazon a kiszolgálón telepítve kell. Kattintson a **Tallózás** mellett az SSL tanúsítvány mezőbe, és válassza ki a biztonságos kapcsolat használandó tanúsítványt.
5. Kattintson a **Hozzáadás** parancsra.
6. Az LDAP-ügyfél hozzáadása párbeszédpanelen adja meg az IP-cím, a készülék, a kiszolgáló vagy az alkalmazás, amely hitelesíti a kiszolgáló és az alkalmazás neve (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
7. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Felhasználók jelentős számú nem még importálva lettek a kiszolgálón és/vagy nem érvényes a kétlépéses ellenőrzést, ha üresen hagyja a nincs bejelölve. Tekintse meg a multi-factor Authentication kiszolgáló súgófájl további információ a szolgáltatásról.

További LDAP-ügyfelek hozzáadásához ismételje meg ezeket a lépéseket.

### <a name="configure-the-ldap-directory-connection"></a>Az LDAP-címtár kapcsolatának konfigurálása

Ha az Azure Multi-Factor Authentication LDAP-hitelesítések fogadására van konfigurálva, a hitelesítéseket az LDAP-címtárnak kell proxykapcsolaton keresztül átadnia. Ezért a Cél lapon csak egyetlen, szürkén megjelenő lehetőség látható LDAP-cél használatához.

1. Az LDAP-címtár kapcsolatának konfigurálásához kattintson a **Címtár-integráció** ikonra.
2. A Beállítások lapon jelölje be a **Megadott LDAP-konfiguráció használata** választógombot.
3. Válassza a **Szerkesztés…** elemet.
4. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az LDAP-címtárhoz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az Azure Multi-Factor Authentication-kiszolgáló súgófájljában találja.

    ![Címtár-integráció](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)

5. Tesztelje az LDAP-kapcsolatot a **Tesztelés** gombra kattintva.
6. Ha az LDAP-kapcsolat tesztelése sikeres volt, kattintson az **OK** gombra.
7. Kattintson a **Szűrők** fülre. A kiszolgáló előre konfigurálva van a tárolók, biztonsági csoportok és felhasználók az Active Directoryból való betöltésére. Másik LDAP-címtárhoz való kötés esetén valószínűleg szerkesztenie kell a megjelenített szűrőket. A szűrőkkel kapcsolatos további információért kattintson a **Súgó** hivatkozásra.
8. Kattintson az **Attribútumok** fülre. A kiszolgáló előre konfigurálva van az Active Directoryból származó attribútumok leképezésére.
9. Másik LDAP-címtárhoz való kötéshez vagy az előre konfigurált attribútumleképezések módosításához kattintson a **Szerkesztés…** elemre
10. Az Attribútumok szerkesztése párbeszédpanelen módosítsa az LDAP-attribútumleképezéseket a címtárra vonatkozóan. Az attribútumneveket beírhatja, vagy kiválaszthatja a mezők melletti **…** gombra kattintva. Az attribútumokkal kapcsolatos további információért kattintson a **Súgó** hivatkozásra.
11. Kattintson az **OK** gombra.
12. Kattintson a **Vállalati beállítások** ikonra, és válassza a **Felhasználónév feloldása** fület.
13. Ha az Active Directoryhoz egy tartományhoz csatlakoztatott kiszolgálóról csatlakozik, hagyja bejelölve a **Windows biztonsági azonosítók (SID-k) használata a felhasználónevek egyeztetéséhez** választógombot. Egyéb esetben jelölje be az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** választógombot. 

Ha az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** lehetőséget bejelöli, az Azure Multi-Factor Authentication-kiszolgáló a felhasználóneveket megkísérli egy egyedi azonosítóvá feloldani az LDAP-címtárban. A rendszer végrehajt egy LDAP-keresést a Címtár-integráció -> Attribútumok lapon meghatározott felhasználónév-attribútumokon. Amikor egy felhasználó hitelesíti magát, a felhasználónév oldja fel a rendszer az LDAP-címtárban szereplő egyedi azonosítóra. Az egyedi azonosítót kell használni a felhasználó az Azure multi-factor Authentication adatfájlban egyeztetéséhez. Ez lehetővé teszi a nem betűérzékeny összehasonlítást, és a hosszú és rövid felhasználónév-formátumokat.

Miután elvégezte ezeket a lépéseket, a multi-factor Authentication kiszolgáló LDAP hozzáférési kérelmeket a konfigurált ügyfelek a konfigurált portokat figyeli, és ezeket a kérelmeket, az LDAP-címtárhoz a hitelesítéshez a proxyként működő.

## <a name="configure-ldap-client"></a>LDAP-ügyfél konfigurálása
Az LDAP-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a készüléket, kiszolgálót vagy alkalmazást, hogy úgy végezzen hitelesítést LDAP-n keresztül az Azure Multi-Factor Authentication-kiszolgálón, mintha az az Ön LDAP-címtára lenne. Használja ugyanazokat a beállításokat, amelyeket általában használna az LDAP-címtárához való közvetlen csatlakozáshoz, azonban az Azure Multi-Factor Authentication-kiszolgáló kiszolgálónevét vagy IP-címét adja meg.
* Konfigurálja az LDAP-időtúllépés 30 – 60 másodperc, hogy a felhasználói hitelesítő adatokat az LDAP-címtár ellenőrzéséhez a második lépés ellenőrzés, azok választ kapnak, és az LDAP-hozzáférési kérelem válaszolni idő.
* Ha LDAPS-t használ, az LDAP-lekérdezéseket kezdeményező készüléknek vagy kiszolgálónak meg kell bíznia az Azure Multi-Factor Authentication-kiszolgálón telepített SSL-tanúsítványban.

