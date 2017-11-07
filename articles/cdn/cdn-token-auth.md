---
title: "Tokent használó hitelesítés az Azure CDN eszközök védelmének biztosítása |} Microsoft Docs"
description: "Megtudhatja, hogyan használja a tokent használó hitelesítés az Azure CDN eszközökhöz való hozzáférést."
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
ms.date: 11/03/2017
ms.author: mezha
ms.openlocfilehash: 700f4c49bbcda1eccbcc7eafc703e625697fa2b4
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Tokent használó hitelesítés az Azure Content Delivery Network eszközök védelme

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés

Jogkivonat hitelesítési egy olyan mechanizmus, amely lehetővé teszi, hogy az Azure Content Delivery Network (CDN) megakadályozza a jogosulatlan ügyfelek szolgáló eszközök. Jogkivonat hitelesítési általában történik, amelyben egy másik webhelyre, gyakran egy üzenet üzenőfalon, az eszközök engedélye nélkül használ a tartalom "hotlinking" megelőzése érdekében. Hotlinking hatással lehetnek a továbbítási költségeit. A CDN tokent használó hitelesítés engedélyezése esetén kérések hitelesítése CDN peremhálózati POP előtt a CDN továbbítja a tartalmat. 

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

A következő munkafolyamat-ábra ismerteti, hogyan a CDN jogkivonat-hitelesítést használ a webes alkalmazás használata.

![CDN-tokent használó hitelesítés munkafolyamat](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN-végpont logika jogkivonatok érvényesség-ellenőrzése
    
Az alábbi folyamatábra bemutatja, miként Azure CDN ellenőrzi az ügyfél kérésében Ha tokent használó hitelesítés konfigurálva van a CDN-végponthoz.

![CDN-token ellenőrzési logika](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Token hitelesítés beállítása

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a CDN-profilt, és kattintson a **kezelése** elindíthatja a kiegészítő portálon.

    ![CDN-profil Manage gomb](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Vigye **HTTP nagy**, és kattintson a **jogkivonat hitelesítési** az a menü. Majd állíthatja be a titkosítási kulcsot és a titkosítási paraméterek az alábbiak szerint:

    1. Adjon meg egy egyedi titkosítási kulcsot a a **elsődleges kulcs** mezőbe, majd adja meg a kulcsot egy biztonsági másolatból a **biztonsági mentési kulcs** mezőbe.

        ![CDN-jogkivonat hitelesítési telepítési kulcs](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. A titkosítás eszközzel titkosítási paraméterek beállítása. A titkosítás eszközzel akkor engedélyezheti vagy tagadhatja meg kérelmet lejárati időt, ország, hivatkozó, protokoll és ügyfél IP-Címmel (tetszőleges kombinációját) alapján. 

        ![CDN eszköz titkosítása](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Adja meg az értékeket egy vagy több, a következő titkosítási paraméterek a **eszköz titkosítása** területen:  

       - **ec_expire**: lejárati idő rendeli jogkivonatot, amely után a jogkivonat lejár. A rendszer megtagadja a lejárati idő után küldött kérelmeket. Ezt a paramétert használ egy Unix Timestamp értéket, a standard epoch óta eltelt percek száma alapján `1/1/1970 00:00:00 GMT`. (Segítségével online eszközök téli idő és a Unix idő közötti átváltásra.) Például, ha azt szeretné, hogy az elévülés token `12/31/2016 12:00:00 GMT`, használja az Unix timestamp értéket `1483185600`, az alábbiak szerint. 
    
         ![CDN ec_expire – példa](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
       - **ec_url_allow**: lehetővé teszi testre szabni a tokenek egy adott eszköz vagy az elérési út. Hogy korlátozza a hozzáférést a kérések megadott relatív elérési úttal rendelkező start URL-cím. URL-címei kis-és nagybetűket. Több elérési utat adjon meg mindegyik elérési út vesszővel elválasztva. A követelményeitől függően állíthat be eltérő értékeket eltérő szintű hozzáférés biztosításához. 
        
         Például, ha az URL-cím `http://www.mydomain.com/pictures/city/strasbourg.png`, ezek a kérelmek engedélyezettek a következő bemeneti értékeket:

         - Adjon meg értéket `/`: minden kérelmek engedélyezettek.
         - Adjon meg értéket `/pictures`, a következő kérelmek engedélyezettek:
            - `http://www.mydomain.com/pictures.png`
            - `http://www.mydomain.com/pictures/city/strasbourg.png`
            - `http://www.mydomain.com/picturesnew/city/strasbourgh.png`
         - Adjon meg értéket `/pictures/`: csak a tartalmazó kéri a `/pictures/` elérési út használata engedélyezett. Például: `http://www.mydomain.com/pictures/city/strasbourg.png`.
         - Adjon meg értéket `/pictures/city/strasbourg.png`: a megadott elérési útra és az eszköz engedélyezett csak kéri.
    
       - **ec_country_allow**: csak lehetővé teszi, hogy egy vagy több megadott országokból kérelmekkel. Más országokból kérelmekkel a rendszer megtagadja. Országhívó számokat használja, és mindegyiket egy-egy vesszővel külön. Például ha azt szeretné, csak az Egyesült Államokban és Franciaország engedélyezése, adjon meg VELÜNK, a mezőben FR az alábbiak szerint.  
        
           ![CDN ec_country_allow – példa](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

       - **ec_country_deny**: egy vagy több megadott országokból kérelmekkel megtagadja. Más országokból kérelmekkel engedélyezettek. Országhívó számokat használja, és mindegyiket egy-egy vesszővel külön. Például ha azt szeretné, hogy megtagadja a hozzáférést az Amerikai Egyesült Államokban és Franciaország, adjon meg VELÜNK, FR a mezőbe.
    
       - **ec_ref_allow**: csak a megadott hivatkozó lehetővé teszi a kérelmek. A hivatkozó azonosítja a weblap, amely csatolva van a kért erőforrás URL-CÍMÉT. Tartalmazza a protokollt a hivatkozó paraméter értéke. A paraméter értéke a következő típusú bemeneti engedélyezettek:
           - Egy állomásnevet vagy egy állomásnevet és egy elérési utat.
           - Több hivatkozó kérelmei. Több hivatkozó kérelmei hozzáadásához külön minden hivatkozó vesszővel válassza el. Ha hivatkozó értéket adjon meg, de a hivatkozó adatokat, az nem küldi el a kérést, mert a böngésző konfigurációs, ezeket a kérelmeket, alapértelmezés szerint sem kap. 
           - Kérések hivatkozó adatok hiányoznak. Az ilyen típusú kérések engedélyezéséhez adja meg a szöveg "Hiányzó", vagy adjon meg egy üres értéket. 
           - Altartományok. Altartományok engedélyezéséhez adja meg a csillag (\*). Ahhoz például, hogy engedélyezi az összes altartomány `consoto.com`, adja meg `*.consoto.com`. 
           
          A következő példa bemutatja, engedélyezze a hozzáférést a kérelmeinek bemeneti `www.consoto.com`, az összes altartomány `consoto2.com`, és üres vagy hiányzó hivatkozó kérelmei rendelkező kérelmek esetében.
        
          ![CDN ec_ref_allow – példa](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
       - **ec_ref_deny**: a megadott hivatkozó megtagadja. A megvalósítás megegyezik a ec_ref_allow paraméter.
         
       - **ec_proto_allow**: csak lehetővé teszi, hogy a megadott protokoll érkező kérelmeket. Például a HTTP vagy HTTPS.
            
       - **ec_proto_deny**: a megadott protokoll megtagadja. Például a HTTP vagy HTTPS.
    
       - **ec_clientip**: korlátozza a hozzáférést a megadott kérelmező IP-címet. IPV4 és IPV6 használata támogatott. A kérelem egyetlen IP-cím vagy IP-alhálózatot adhat meg.
            
         ![CDN ec_clientip – példa](./media/cdn-token-auth/cdn-token-auth-clientip.png)

    3. Miután befejezte a titkosítási paramétert értékek megadása, válassza ki a kulcs titkosításához (ha az elsődleges és a kulcsot egy biztonsági másolatból hozott létre) az a **kulcs titkosításához** listájában, a titkosítási verziójú a  **Titkosítási verziója** listában, majd kattintson az **titkosítása**.
        
    4. Lehetősége van ellenőrizni a jogkivonatot a visszafejtés eszközzel. Illessze be a token értékét a **visszafejtése tokenjét** mezőbe. Válassza ki a titkosítási kulcs visszafejtése a a **kulcs visszafejtése** legördülő listában, majd kattintson az **visszafejtéséhez**.

    5. Másik lehetőségként testreszabása eredményül, ha a rendszer megtagadja a kérelmet válaszkód típusú. Jelölje be a kódot a **válaszkód** legördülő listából válassza ki, és kattintson **mentése**. A **403** válaszának kódja (tiltott) alapértelmezettként van beállítva. Az egyes válaszkódot, is megadhat a a hibalap URL-CÍMÉT a **Fejlécérték** mezőbe. 

    6. Miután létrehozta a titkosított tokent, akkor fűzi azokat a fájl végére lekérdezési karakterláncként az URL-cím elérési úthoz. Például: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

3. A **HTTP nagy**, kattintson a **szabálymotor**. A szabályok motor használatával alkalmazza a szolgáltatás, a jogkivonat hitelesítési szolgáltatás engedélyezése és hitelesítésre vonatkozó további token képességek engedélyezése elérési utak megadása. További információkért lásd: [szabályok motor hivatkozás](cdn-rules-engine-reference.md).

    1. Jelöljön ki egy meglévő szabályt, vagy hozzon létre egy új szabályt, amely a token hitelesítést alkalmazni kívánt eszköz vagy elérési út megadása. 
    2. Egy szabály tokent használó hitelesítés engedélyezéséhez jelölje be  **[jogkivonat hitelesítési](cdn-rules-engine-reference-features.md#token-auth)**  a a **szolgáltatások** legördülő listában, majd válassza a **engedélyezve**. Kattintson a **frissítés** szabály frissítésekor vagy **Hozzáadás** szabály létrehozásakor.
        
    ![CDN szabályok motor tokent használó hitelesítés engedélyezése – példa](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. A szabályok motor további jogkivonat hitelesítési szolgáltatásokkal is engedélyezheti. A következő funkciók engedélyezéséhez válassza ki azt a **szolgáltatások** legördülő listában, majd válasszon **engedélyezve**.
    
    - **[A token hitelesítés megtagadása kód](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: határozza meg a választ a felhasználó eredményül, amikor a rendszer megtagadja a kérelmet. Az itt szabályok felülbírálása beállított válaszkód a **egyéni Megtagadás kezelése** szakasz a jogkivonat-alapú hitelesítés oldalon.
    - **[A token Auth figyelmen kívül hagyása URL-cím esetében](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: meghatározza, hogy a jogkivonat érvényesítésére használt URL-cím kis-és nagybetűket.
    - **[A token Auth paraméter](cdn-rules-engine-reference-features.md#token-auth-parameter)**: átnevezi a jogkivonat hitelesítési lekérdezési karakterláncot, amely a kért URL-cím jelenik meg. 
        
    ![CDN-szabályok motor-tokent használó hitelesítés beállításai – példa](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Testre szabhatja a jogkivonatot a forráskód [GitHub](https://github.com/VerizonDigital/ectoken).
Rendelkezésre álló nyelvek:
    
- C#
- C#
- PHP
- Perl
- Java
- Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Az Azure CDN szolgáltatásai és szolgáltató díjszabása

További információ: [CDN áttekintésével](cdn-overview.md).
