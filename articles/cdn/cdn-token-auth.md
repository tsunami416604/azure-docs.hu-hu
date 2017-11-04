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
ms.openlocfilehash: b1bcaf14e9805afa82c765092b62201f58c7cbc4
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Tokent használó hitelesítés az Azure Content Delivery Network eszközök védelme

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés

Jogkivonat hitelesítési egy olyan mechanizmus, amely lehetővé teszi, hogy az Azure Content Delivery Network (CDN) megakadályozza a jogosulatlan ügyfelek szolgáló eszközök. Jogkivonat hitelesítési általában történik, amelyben egy másik webhelyre, gyakran egy üzenet üzenőfalon, az eszközök engedélye nélkül használ a tartalom "hotlinking" megelőzése érdekében. Hotlinking hatással lehetnek a továbbítási költségeit. Ha engedélyezi ezt a funkciót a CDN, kérések hitelesítése CDN peremhálózati POP előtt a CDN továbbítja a tartalmat. 

## <a name="how-it-works"></a>Működés

Jogkivonat hitelesítési ellenőrzi a kérelmek azzal, hogy a token értékét tartalmazó kérelmeket, hogy a kérelmező tartás kódolt információt a megbízható helyek által generált. Tartalom kiszolgált egy kérelmezőnek csak akkor, ha a kódolt információ megfelel a követelményeknek; Ellenkező esetben kérelmeket a rendszer megtagadja. A követelmények beállítása a következő paraméterek közül:

- Ország: Engedélyezi vagy megtagadja a megadott országokból kérelmekkel azok [országhívószám](https://msdn.microsoft.com/library/mt761717.aspx).
- URL-címe: Csak vonatkozó kérés engedélyezése, amelyek megfelelnek a megadott eszköz vagy az elérési út.
- Állomás: Engedélyezi vagy megtagadja a hozzáférést a megadott gazdagépek használata a kérelem fejlécében kérelmek.
- Hivatkozó: Engedélyezése vagy megtagadása – a megadott hivatkozó kérelem.
- IP-cím: csak az adott IP-cím vagy IP-alhálózat származó kérelmek.
- Protokoll: Engedélyezi vagy megtagadja a hozzáférést a tartalom kérésére protokollon alapuló kérelmek.
- Lejárati idő: rendelje hozzá a dátum és idő ponttal annak érdekében, hogy a hivatkozás csak korlátozott ideig időszakra érvényes marad.

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

    1. Adjon meg egy egyedi titkosítási kulcsot a **elsődleges kulcs** , és adja meg egy másikat a **biztonsági mentés kulcs**.

        ![CDN-jogkivonat hitelesítési telepítési kulcs](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. A titkosítás eszközzel titkosítási paraméterek beállítása. A titkosítás eszközzel akkor engedélyezheti vagy tagadhatja meg kérelmet lejárati időt, ország, hivatkozó, protokoll és ügyfél IP-Címmel (tetszőleges kombinációját) alapján.

        ![CDN eszköz titkosítása](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       - ec_expire: lejárati idő rendeli jogkivonatot, amely után a jogkivonat lejár. A rendszer megtagadja a lejárati idő után küldött kérelmeket. Ezt a paramétert használ egy Unix Timestamp értéket, a standard epoch óta eltelt percek száma alapján `1/1/1970 00:00:00 GMT`. (Segítségével online eszközök téli idő és a Unix idő közötti átváltásra.) Például, ha azt szeretné, hozzon létre a token elévülés `12/31/2016 12:00:00 GMT`, használja az Unix timestamp értéket `1483185600`, az alábbiak szerint. 
    
         ![CDN ec_expire – példa](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
       - ec_url_allow: lehetővé teszi testre szabni a tokenek egy adott eszköz vagy az elérési út. Hogy korlátozza a hozzáférést a kérések megadott relatív elérési úttal rendelkező start URL-cím. URL-címei kis-és nagybetűket. Több elérési utat adjon meg mindegyik elérési út vesszővel elválasztva. A követelményeitől függően állíthat be eltérő értékeket eltérő szintű hozzáférés biztosításához. 
        
          Például, ha az URL-cím `http://www.mydomain.com/pictures/city/strasbourg.png`, az alábbiak szerint állíthatja a bemeneti érték és a hozzáférési szintet:

           - Adjon meg értéket `"/"`: minden kérelmek engedélyezettek
           - Adjon meg értéket `"/pictures`. A következő kérelmek engedélyezettek:
              - `http://www.mydomain.com/pictures.png`
              - `http://www.mydomain.com/pictures/city/strasbourg.png`
              - `http://www.mydomain.com/picturesnew/city/strasbourgh.png`
            - Adjon meg értéket `/pictures/`: csak a vonatkozó kérések `/pictures/` engedélyezettek. Például: `http://www.mydomain.com/pictures/city/strasbourg.png`.
            - Adjon meg értéket `/pictures/city/strasbourg.png`: csak az adott eszköz kérelmek engedélyezettek.
    
          ![CDN ec_url_allow – példa](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
       - ec_country_allow: csak lehetővé teszi, hogy egy vagy több megadott országokból kérelmekkel. Más országokból kérelmekkel a rendszer megtagadja. Országhívó számokat használja, és mindegyiket egy-egy vesszővel külön. Például ha azt szeretné, csak az Egyesült Államokban és Franciaország engedélyezése, adjon meg VELÜNK, a mezőben FR az alábbiak szerint.  
        
           ![CDN ec_country_allow – példa](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

       - ec_country_deny: egy vagy több megadott országokból kérelmekkel megtagadja. Más országokból kérelmekkel engedélyezettek. Országhívó számokat használja, és mindegyiket egy-egy vesszővel külön. Például ha azt szeretné, hogy megtagadja a hozzáférést az Amerikai Egyesült Államokban és Franciaország, adjon meg VELÜNK, a mezőben FR.
    
       - ec_ref_allow: csak a megadott hivatkozó lehetővé teszi a kérelmek. A hivatkozó azonosítja a weblap, amely csatolva van a kért erőforrás URL-CÍMÉT. Tartalmazza a protokollt a hivatkozó paraméter értéke. A paraméter értéke a következő típusú bemeneti engedélyezettek:
           - Egy állomásnevet vagy egy állomásnevet és egy elérési utat.
           - Több hivatkozó kérelmei. Több hivatkozó kérelmei hozzáadásához külön minden hivatkozó vesszővel válassza el. Ha hivatkozó értéket adjon meg, de a hivatkozó adatokat, az nem küldi el a kérést, mert a böngésző konfigurációs, ezeket a kérelmeket, alapértelmezés szerint sem kap. 
           - Kérések hivatkozó adatok hiányoznak. Az ilyen típusú kérések engedélyezéséhez adja meg a szöveg "Hiányzó", vagy adjon meg egy üres értéket. 
           - Altartományok. Altartományok engedélyezéséhez adja meg a csillag (*). Ahhoz például, hogy engedélyezi az összes altartomány `consoto.com` meg `*.consoto.com`. 
           
          A következő példa bemutatja, engedélyezze a hozzáférést a kérelmeinek bemeneti `www.consoto.com`, az összes altartomány `consoto2.com`, és üres vagy hiányzó hivatkozó kérelmei rendelkező kérelmek esetében.
        
          ![CDN ec_ref_allow – példa](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
       - ec_ref_deny: a megadott hivatkozó megtagadja. A megvalósítás megegyezik a ec_ref_allow paraméter.
         
       - ec_proto_allow: csak lehetővé teszi, hogy a megadott protokoll érkező kérelmeket. Például a HTTP vagy HTTPS.
        
         ![CDN ec_proto_allow – példa](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
       - ec_proto_deny: a megadott protokoll megtagadja. Például a HTTP vagy HTTPS.
    
       - ec_clientip: korlátozza a hozzáférést a megadott kérelmező IP-címet. IPV4 és IPV6 használata támogatott. A kérelem egyetlen IP-cím vagy IP-alhálózatot adhat meg.
            
         ![CDN ec_clientip – példa](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. Lehetősége van ellenőrizni a jogkivonatot a leírás eszközzel.

    4. Másik lehetőségként testreszabása a válaszának típusa, amelyet ad vissza, ha a rendszer megtagadja a kérelmet. Alapértelmezés szerint a 403-as válaszkódot szolgál.

3. A **HTTP nagy**, kattintson a **szabálymotor**. A szabályok motor használatával alkalmazza a szolgáltatás, a jogkivonat hitelesítési szolgáltatás engedélyezése és hitelesítésre vonatkozó további token képességek engedélyezése elérési utak megadása.

    1. Használja a **IF** határozza meg az eszköz vagy elérési út tokent használó hitelesítés alkalmazni kívánt oszlop. 
    2. Tokent használó hitelesítés engedélyezéséhez jelölje be **jogkivonat hitelesítési** a a **szolgáltatások** legördülő menüből.
        
    ![CDN szabályok motor tokent használó hitelesítés engedélyezése – példa](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. A szabályok összetevőben van néhány további funkciókat is engedélyeznie:
    
    - **A token hitelesítés megtagadása kód**: határozza meg a választ a felhasználó eredményül, amikor a rendszer megtagadja a kérelmet. Az itt szabályok felülbírálása Megtagadás kód beállítását a **jogkivonat hitelesítési** fülre.
    - **A token Auth figyelmen kívül hagyása URL-cím esetében**: meghatározza, hogy a jogkivonat érvényesítésére használt URL-cím kis-és nagybetűket.
    - **A token Auth paraméter**: átnevezi a jogkivonat hitelesítési lekérdezési karakterláncot, amely a kért URL-cím jelenik meg. 
        
    ![CDN-szabályok motor-tokent használó hitelesítés beállításai – példa](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Testre szabhatja a jogkivonatot, amely az alkalmazás által generált jogkivonat-alapú hitelesítési szolgáltatások lexikális eleme. Forráskód érhetők el [GitHub](https://github.com/VerizonDigital/ectoken).
Rendelkezésre álló nyelvek:
    
    - C#
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Az Azure CDN szolgáltatásai és szolgáltató díjszabása

További információ: [CDN áttekintésével](cdn-overview.md).
