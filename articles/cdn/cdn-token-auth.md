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
ms.openlocfilehash: 88956b324a543c5347e16b1278f6b2179a3b9c24
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Tokent használó hitelesítés az Azure Content Delivery Network eszközök védelme

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés

Jogkivonat hitelesítési egy olyan mechanizmus, amely lehetővé teszi, hogy az Azure Content Delivery Network (CDN) megakadályozza a jogosulatlan ügyfelek szolgáló eszközök. Jogkivonat hitelesítési általában történik, amelyben egy másik webhelyre, gyakran egy üzenet üzenőfalon, az eszközök engedélye nélkül használ a tartalom "hotlinking" megelőzése érdekében. Hotlinking hatással lehetnek a továbbítási költségeit. Ha engedélyezi ezt a funkciót a CDN, kérések hitelesítése CDN peremhálózati POP előtt a CDN továbbítja a tartalmat. 

## <a name="how-it-works"></a>Működés

Jogkivonat hitelesítési ellenőrzi a kérelmek azzal, hogy a token értékét tartalmazó kérelmeket, hogy a kérelmező tartás kódolt információt a megbízható helyek által generált. Tartalom kiszolgált egy kérelmezőnek csak akkor, ha a kódolt információ megfelel a követelményeknek; Ellenkező esetben kérelmeket a rendszer megtagadja. A követelmények beállítása a következő paraméterek közül:

- Ország: engedélyezi vagy megtagadja a hozzáférést meghatározott országokból származó kérelmek.  [Érvénytelen országhívó számokat listája.](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL-címe: csak a megadott eszköz vagy elérési út engedélyezése.  
- Állomás: engedélyezi vagy megtagadja a hozzáférést a kérelem fejlécében megadott állomás használatát kérelmek.
- Hivatkozó: engedélyezése vagy megtagadása megadott hivatkozó kéréséhez.
- IP-cím: engedélyezése csak a megadott IP-cím vagy IP-alhálózat származó kérelmek.
- Protokoll: engedélyezi, vagy a tartalom kérésére protokollon alapuló-kérések blokkolása.
- Lejárati idő: rendelhet annak érdekében, hogy a hivatkozás csak érvényes marad egy korlátozott ideig dátum és idő időtartamot.

További információkért lásd: a részletes konfigurációs példák mindegyik paraméterére vonatkozóan [beállítása a tokent használó hitelesítés](#setting-up-token-authentication).

Egy titkosított jogkivonat létrehozása után, az útvonal URL-cím végére lekérdezési karakterláncként fűznünk. Például: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

## <a name="reference-architecture"></a>Referenciaarchitektúra

A következő munkafolyamat-ábra ismerteti, hogyan a CDN jogkivonat-hitelesítést használ a webes alkalmazás használható.

![CDN-tokent használó hitelesítés munkafolyamat](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN-végpont logika jogkivonatok érvényesség-ellenőrzése
    
Az alábbi folyamatábra bemutatja, miként Azure CDN ellenőrzi az ügyfél kérésében Ha tokent használó hitelesítés konfigurálva van a CDN-végponthoz.

![CDN-token ellenőrzési logika](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Token hitelesítés beállítása

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a CDN-profilt, és kattintson a **kezelése** elindíthatja a kiegészítő portálon.

    ![CDN-profil Manage gomb](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Vigye **HTTP nagy**, és kattintson a **jogkivonat hitelesítési** az a menü. Beállította a titkosítási kulcsot és a titkosítási paraméterek ezen a lapon.

    1. Adjon meg egy egyedi titkosítási kulcsot a **elsődleges kulcs**.  Adjon meg egy másikat a **kulcs biztonsági mentése**

        ![CDN-jogkivonat hitelesítési telepítési kulcs](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. A titkosítási eszközével titkosítási paraméterek beállítása (engedélyezi vagy megtagadja a kérelmet az alapján a lejárati idő, ország, hivatkozó, protokoll, ügyfél IP. Használható bármilyen kombinációja.)

        ![CDN eszköz titkosítása](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - EK-lejár: hozzárendel egy megadott időszak után a jogkivonat lejárati időt. A rendszer megtagadja a lejárati idő után küldött kérelmeket. Ezt a paramétert használ Unix időbélyeg (1/1/1970 szabványos epoch óta eltelt percek alapján 00:00:00 GMT. Segítségével online eszközök adja meg a standard és a Unix idő közötti konverzió.) Például, ha azt szeretné, hozzon létre a token elévülés `12/31/2016 12:00:00 GMT`, használja az Unix timestamp értéket `1483185600`, az alábbiak szerint.
    
        ![CDN ec_expire – példa](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - EK URL-cím engedélyezése: lehetővé teszi testre szabni a tokenek egy adott eszköz vagy az elérési út. Hogy korlátozza a hozzáférést a kérések megadott relatív elérési úttal rendelkező start URL-cím. Mindegyik elérési út vesszővel elválasztva több elérési utat adjon meg. URL-címei kis-és nagybetűket. Attól függően, hogy az a követelmény állíthat be eltérő érték eltérő szintű hozzáférés biztosításához. Az alábbiakban néhány forgatókönyv:
        
            Ha egy URL-cím: http://www.mydomain.com/pictures/city/strasbourg.png. Lásd: a bemeneti érték "", és ennek megfelelően. a hozzáférési szint

            1. Adjon meg értéket "/": minden kérelemhez engedélyezett lesz
            2. Adjon meg értéket "/ képek": a következő kérelmeket lesz engedélyezése
            
                - http://www.mydomain.com/Pictures.png
                - http://www.mydomain.com/Pictures/City/Strasbourg.png
                - http://www.mydomain.com/picturesnew/City/strasbourgh.png
            3. Adjon meg értéket "/ képek /": csak a /pictures/ lesz engedélyezett kérelmek
            4. Adjon meg értéket "/ pictures/city/strasbourg.png": csak az eszköz lehetővé teszi, hogy kérelem
    
        ![CDN ec_url_allow – példa](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - EK ország engedélyezése: csak lehetővé teszi, hogy egy vagy több megadott országokból kérelmekkel. Más országokból kérelmekkel elutasításra kerülne. Országkód segítségével állítsa be a paramétereket, és minden egyes országhívó szám vesszővel elválasztva. Például ha engedélyezi a hozzáférést az Amerikai Egyesült Államokban és Franciaország, adjon meg VELÜNK, FR az oszlop alatt.  
        
        ![CDN ec_country_allow – példa](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - EK ország elutasítás: egy vagy több megadott országokból származó megtagadja. Más országokból kérelmekkel engedélyezett lesz. Országkód segítségével állítsa be a paramétereket, és minden egyes országhívó szám vesszővel elválasztva. Például ha azt szeretné, hogy megtagadja a hozzáférést az Amerikai Egyesült Államokban és Franciaország, adjon meg VELÜNK, FR oszlopában.
    
        - ec_ref_allow: lehetővé teszi a kérelmek a csak a megadott hivatkozó. A hivatkozó azonosítja a weblap, amely csatolva van a kért erőforrás URL-CÍMÉT. Tartalmazza a protokollt a hivatkozó paraméter értéke. A paraméter értéke a következő típusú bemeneti engedélyezettek:
           - Egy állomásnevet vagy egy állomásnevet és egy elérési utat.
           - Több hivatkozó kérelmei. Több hivatkozó kérelmei hozzáadásához külön minden hivatkozó vesszővel válassza el. Ha hivatkozó értéket adjon meg, de a hivatkozó adatokat, az nem küldi el a kérést, mert a böngésző konfigurációs, ezeket a kérelmeket, alapértelmezés szerint sem kap. 
           - Kérések hivatkozó adatok hiányoznak. Az ilyen típusú kérések engedélyezéséhez adja meg a szöveg "Hiányzó", vagy adjon meg egy üres értéket. 
           - Altartományok. Altartományok engedélyezéséhez adja meg a csillag (*). Ahhoz például, hogy engedélyezi az összes altartomány `consoto.com` meg `*.consoto.com`. 
           
          A következő példa bemutatja, engedélyezze a hozzáférést a kérelmeinek bemeneti `www.consoto.com`, az összes altartomány `consoto2.com`, és üres vagy hiányzó hivatkozó kérelmei rendelkező kérelmek esetében.
        
          ![CDN ec_ref_allow – példa](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - EK ref elutasítás: a megadott hivatkozó megtagadja. Részletek és a "EK-ref-engedélyezése" paraméterben példa hivatkozik.
         
        - EK protokoll engedélyezése: csak lehetővé teszi, hogy a megadott protokoll érkező kérelmeket. Például a http vagy https.
        
        ![CDN ec_proto_allow – példa](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - EK protokoll elutasítás: a megadott protokoll megtagadja. Például a http vagy https.
    
        - EK-ügyfélip: korlátozza a hozzáférést a megadott kérelmező IP-címet. IPV4 és IPV6 használata támogatott. Megadhat egy kérelemhez IP-cím vagy IP-alhálózat.
            
        ![CDN ec_clientip – példa](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. A leírás eszközzel a token tesztelheti.

    4. Testre szabhatja a válaszának típusa, amelyet a rendszer visszairányítja a(z) felhasználói kérelem megtagadása. Alapértelmezés szerint 403 használjuk.

3. Most kattintson **szabálymotor** lap **HTTP nagy**. Ezen a lapon megadhatók a funkció alkalmazása, a jogkivonat hitelesítési szolgáltatás engedélyezése és hitelesítésre vonatkozó további token képességek engedélyezése elérési útjai használandó.

    - "IF" oszlop segítségével határozza meg az eszköz vagy az elérési utat, amely a token hitelesítést alkalmazni szeretné. 
    - Ide kattintva vehet fel a "Token Auth" token hitelesítés engedélyezése a szolgáltatás legördülő listából.
        
    ![CDN szabályok motor tokent használó hitelesítés engedélyezése – példa](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Az a **szabálymotor** lap, van néhány további képességeket engedélyezheti.
    
    - A token hitelesítés megtagadása kód: határozza meg a választ, amely során a rendszer megtagadja a kérelmet a felhasználó vissza. Szabályok itt beállítása felülírja a Megtagadás kód beállítását a token hitelesítés lap.
    - Jogkivonat hitelesítési figyelmen kívül hagyja: meghatározza, hogy a jogkivonat érvényesítésére használt URL-cím és a nagybetűk között legyen.
    - Jogkivonat hitelesítési paraméter: a jogkivonat hitelesítési lekérdezési karakterlánc paraméter jelenik meg a kért URL-cím a átnevezése. 
        
    ![CDN-szabályok motor-tokent használó hitelesítés beállításai – példa](./media/cdn-token-auth/cdn-rules-engine2.png)

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
