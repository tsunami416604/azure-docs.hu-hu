---
title: "Tokent használó hitelesítés az Azure CDN eszközök védelmének biztosítása |} Microsoft Docs"
description: "Jogkivonat-hitelesítést használ az Azure CDN eszközökhöz való hozzáférést."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Tokent használó hitelesítés az Azure CDN-eszközök védelme

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>Áttekintés

Jogkivonat hitelesítési egy olyan mechanizmus, amely lehetővé teszi, hogy megakadályozza a Azure CDN szolgáló eszközök jogosulatlan ügyfelekre.  Általában ehhez a tartalom "hotlinking" megelőzése érdekében ahol egy másik webhelyre, gyakran egy üzenet üzenőfalon, használja az eszközök engedély Allowed értékű.  Ez hatással lehetnek a továbbítási költségeit. Ha engedélyezi ezt a funkciót a CDN, CDN peremhálózati POP a tartalom továbbítása előtt kérelmek lesz hitelesítve. 

## <a name="how-it-works"></a>Működés

Tokent használó hitelesítés ellenőrzi a kéréseket a megbízható helyek által generált azzal, hogy a kérelmező kódolt információt tartalmazó token értéket tartalmazó kérelmeket. Tartalom csak kell kézbesíteni kérelmező, amikor a kódolt információt a követelményeknek, ellenkező esetben kérelmeket a rendszer megtagadja. A követelmény egy vagy több, az alábbi paraméterek használatával állíthat be.

- Ország: engedélyezi vagy megtagadja a hozzáférést meghatározott országokból származó kérelmek.  [Érvénytelen országhívó számokat listája.](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL-címe: csak a megadott eszköz vagy elérési út engedélyezése.  
- Állomás: engedélyezi vagy megtagadja a hozzáférést a kérelem fejlécében megadott állomás használatát kérelmek.
- Hivatkozó: engedélyezése vagy megtagadása megadott hivatkozó kéréséhez.
- IP-cím: engedélyezése csak a megadott IP-cím vagy IP-alhálózat származó kérelmek.
- Protokoll: engedélyezi, vagy a tartalom kérésére protokollon alapuló-kérések blokkolása.
- Lejárati idő: rendelhet annak érdekében, hogy a hivatkozás csak érvényes marad egy korlátozott ideig dátum és idő időtartamot.

Tekintse meg mindegyik paraméter részletes konfigurációs példát.

## <a name="reference-architecture"></a>Referenciaarchitektúra

Olvassa el a referencia-architektúrában, hogyan kell beállítani a CDN tokent használó hitelesítés működéséhez a webalkalmazással.

![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN-végpont logika jogkivonatok érvényesség-ellenőrzése
    
Ez a diagram bemutatja, miként Azure CDN ellenőrzi az ügyfél kérésében Ha tokent használó hitelesítés konfigurálva van a CDN-végponthoz.

![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Token hitelesítés beállítása

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a CDN-profilt, és kattintson a **kezelése** gombra kattintva indítsa el a kiegészítő portálon.

    ![CDN-profil panelje kezelése gomb](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Vigye **HTTP nagy**, és kattintson a **jogkivonat hitelesítési** az a menü. Titkosítási kulcs és a titkosítási paraméterek ezen a lapon fog beállítani.

    1. Adjon meg egy egyedi titkosítási kulcsot a **elsődleges kulcs**.  Adjon meg egy másikat a **kulcs biztonsági mentése**

        ![CDN-jogkivonat hitelesítési telepítési kulcs](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. A titkosítási eszközével titkosítási paraméterek beállítása (engedélyezi vagy megtagadja a kérelmet az alapján a lejárati idő, ország, hivatkozó, protokoll, ügyfél IP. Használható bármilyen kombinációja.)

        ![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - EK-lejár: hozzárendel egy megadott időszak után a jogkivonat lejárati időt. Miután a rendszer megtagadja a lejárati idő küldött kérelmeket. Ezt a paramétert használ Unix időbélyeg (1/1/1970 szabványos epoch óta eltelt percek alapján 00:00:00 GMT. Segítségével online eszközök adja meg a standard és a Unix idő közötti konverzió.)  Például, ha azt szeretné, hozzon létre a jogkivonat lejárt, 12, 31/2016 12:00:00 GMT, használja a Unix-idő: 1483185600 az alábbi:
    
        ![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - EK URL-cím engedélyezése: lehetővé teszi testre szabni a tokenek egy adott eszköz vagy az elérési út. Hogy korlátozza a hozzáférést a kérések megadott relatív elérési úttal rendelkező start URL-cím. Mindegyik elérési út vesszővel elválasztva több elérési utat adjon meg. URL-címei kis-és nagybetűket. Attól függően, hogy az a követelmény állíthat be eltérő érték eltérő szintű hozzáférés biztosításához. Az alábbiakban néhány forgatókönyv:
        
            Ha egy URL-cím: http://www.mydomain.com/pictures/city/strasbourg.png. Lásd: a bemeneti érték "", és ennek megfelelően. a hozzáférési szint

            1. Adjon meg értéket "/": minden kérelemhez engedélyezett lesz
            2. Adjon meg értéket "/ képek": a következő kérelmeket lesz engedélyezése
            
                - http://www.mydomain.com/Pictures.png
                - http://www.mydomain.com/Pictures/City/Strasbourg.png
                - http://www.mydomain.com/picturesnew/City/strasbourgh.png
            3. Adjon meg értéket "/ képek /": csak a /pictures/ lesz engedélyezett kérelmek
            4. Adjon meg értéket "/ pictures/city/strasbourg.png": csak az eszköz lehetővé teszi, hogy kérelem
    
        ![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - EK ország engedélyezése: csak lehetővé teszi, hogy egy vagy több megadott országokból kérelmekkel. Más országokból kérelmekkel elutasításra kerülne. Országkód segítségével állítsa be a paramétereket, és minden egyes országhívó szám vesszővel elválasztva. Például ha engedélyezi a hozzáférést az Amerikai Egyesült Államokban és Franciaország, adjon meg VELÜNK, FR az oszlop alatt.  
        
        ![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - EK ország elutasítás: egy vagy több megadott országokból származó megtagadja. Más országokból kérelmekkel engedélyezett lesz. Országkód segítségével állítsa be a paramétereket, és minden egyes országhívó szám vesszővel elválasztva. Például ha azt szeretné, hogy megtagadja a hozzáférést az Amerikai Egyesült Államokban és Franciaország, adjon meg VELÜNK, FR oszlopában.
    
        - EK ref engedélyezése: csak a megadott hivatkozó lehetővé teszi a kérelmek. A hivatkozó azonosítja a kért erőforráshoz kapcsolódó weblap URL-CÍMÉT. A hivatkozó paraméter értéke nem szabad tartalmaznia a protokollt. Az állomásnév és/vagy egy adott elérési utat adott állomásnév a szövegszerkesztőben. Több hivatkozó kérelmei belül mindegyiknél vesszővel elválasztva egyetlen paramétert is hozzáadhat. Ha hivatkozó értéket adta, de a hivatkozó információk nem kerülnek a kérést, mert néhány konfigurációját a, ezek a kérelmek elutasításra kerülne alapértelmezés szerint. Hozzárendelheti a "Hiányzó" vagy üres értéket a paraméter engedélyezi ezeket a kéréseket a hivatkozó adatok hiányoznak. Is "*. consoto.com" consoto.com az összes altartomány engedélyezéséhez.  Ha engedélyezi a hozzáférést a www.consoto.com, consoto2.com és az üres vagy hiányzó reffers erquests altartományokkal érkező kéréseket, például adjon meg értéket az alábbi:
        
        ![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - EK ref elutasítás: a megadott hivatkozó megtagadja. Részletek és a "EK-ref-engedélyezése" paraméterben példa hivatkozik.
         
        - EK protokoll engedélyezése: csak lehetővé teszi, hogy a megadott protokoll érkező kérelmeket. Például a http vagy https.
        
        ![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - EK protokoll elutasítás: a megadott protokoll megtagadja. Például a http vagy https.
    
        - EK-ügyfélip: korlátozza a hozzáférést a megadott kérelmező IP-címet. IPV4 és IPV6 használata támogatott. Megadhat egy kérelemhez IP-cím vagy IP-alhálózat.
            
        ![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. A jogkivonat a visszafejtés eszközzel tesztelheti.

    4. Testre szabhatja a válaszának típusa, amelyet a rendszer visszairányítja a(z) felhasználói kérelem megtagadása. Alapértelmezés szerint 403 használjuk.

3. Most kattintson **szabálymotor** lap **HTTP nagy**. Ezen a lapon megadhatók alkalmazza a szolgáltatás, a jogkivonat hitelesítési funkció engedélyezéséhez, és engedélyezheti a megadott elérési utakon használandó további jogkivonat hitelesítési kapcsolatos képességeit.

    - "IF" oszlop segítségével határozza meg az eszköz vagy az elérési utat, amely a token hitelesítést alkalmazni szeretné. 
    - Ide kattintva vehet fel a "Token Auth" token hitelesítés engedélyezése a szolgáltatás legördülő listából.
        
    ![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Az a **szabálymotor** lap, van néhány további képességeket engedélyezheti.
    
    - A token hitelesítés megtagadása kód: határozza meg a választ, amely során a rendszer megtagadja a kérelmet a felhasználó vissza. Szabályok itt beállítása felülírja a Megtagadás kód beállítását a token hitelesítés lap.
    - Jogkivonat hitelesítési figyelmen kívül hagyja: meghatározza, hogy a jogkivonat érvényesítésére használt URL-cím és a nagybetűk között legyen.
    - Jogkivonat hitelesítési paraméter: a jogkivonat hitelesítési lekérdezési karakterlánc paraméter jelenik meg a kért URL-cím a átnevezése. 
        
    ![CDN-profil panelje kezelése gomb](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Testre szabhatja a jogkivonatot, amely az alkalmazás által generált jogkivonat-alapú hitelesítési szolgáltatások lexikális eleme. Forráskódját itt is elérhetők a [GitHub](https://github.com/VerizonDigital/ectoken).
Rendelkezésre álló nyelvek:
    
    - C#
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Az Azure CDN szolgáltatásai és szolgáltató díjszabása

Tekintse meg a [CDN áttekintésével](cdn-overview.md) témakör.
