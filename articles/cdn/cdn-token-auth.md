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
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: f6d008a92677d28d0184e64637dcb2e093299519
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Tokent használó hitelesítés az Azure Content Delivery Network eszközök védelme

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés

Jogkivonat hitelesítési egy olyan mechanizmus, amely lehetővé teszi, hogy az Azure Content Delivery Network (CDN) megakadályozza a jogosulatlan ügyfelek szolgáló eszközök. Jogkivonat hitelesítési általában történik, amelyben egy másik webhelyre, egy üzenet üzenőfalon, például az eszközök engedélye nélkül használ a tartalom "hotlinking" megelőzése érdekében. Hotlinking hatással lehetnek a továbbítási költségeit. A CDN tokent használó hitelesítés engedélyezése esetén kérések hitelesítése CDN biztonsági kiszolgáló előtt a CDN továbbítja a tartalmat. 

## <a name="how-it-works"></a>Működés

Jogkivonat hitelesítési ellenőrzi, hogy kérelmek által előállított megbízható webhelynek azzal, hogy a token értékét, hogy tartás kódolású igénylő információt tartalmazó kérelmeket. Tartalom kiszolgált egy kérelmezőnek csak akkor, ha a kódolt információ megfelel a követelményeknek; Ellenkező esetben kérelmeket a rendszer megtagadja. A követelmények beállítása a következő paraméterek közül:

- Ország: Engedélyezi vagy megtagadja a megadott országokból kérelmekkel azok [országhívószám](https://msdn.microsoft.com/library/mt761717.aspx).
- URL-címe: Csak vonatkozó kérés engedélyezése, amelyek megfelelnek a megadott eszköz vagy az elérési út.
- Állomás: Engedélyezi vagy megtagadja a hozzáférést a megadott gazdagépek használata a kérelem fejlécében kérelmek.
- Hivatkozó: Engedélyezése vagy megtagadása – a megadott hivatkozó kérelem.
- IP-cím: csak az adott IP-cím vagy IP-alhálózat származó kérelmek.
- Protokoll: Engedélyezi vagy megtagadja a hozzáférést a tartalom kérésére protokollon alapuló kérelmek.
- Lejárati idő: rendelje hozzá a dátum és idő ponttal annak érdekében, hogy a hivatkozás csak korlátozott ideig időszakra érvényes marad.

További információkért lásd: a részletes konfigurációs példák mindegyik paraméterére vonatkozóan [beállítása a tokent használó hitelesítés](#setting-up-token-authentication).

## <a name="reference-architecture"></a>Referenciaarchitektúra

A következő munkafolyamat-ábra ismerteti, hogyan a CDN jogkivonat-hitelesítést használ a webes alkalmazás használata.

![CDN-tokent használó hitelesítés munkafolyamat](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN-végpont logika jogkivonatok érvényesség-ellenőrzése
    
Az alábbi folyamatábra bemutatja, miként Azure CDN ellenőrzi az egyik ügyfélkérelemben Ha tokent használó hitelesítés konfigurálva van a CDN-végponthoz.

![CDN-token ellenőrzési logika](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Token hitelesítés beállítása

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a CDN-profilt, és kattintson a **kezelése** elindíthatja a kiegészítő portálon.

    ![CDN-profil Manage gomb](./media/cdn-token-auth/cdn-manage-btn.png)

2. Vigye **HTTP nagy**, majd kattintson a **jogkivonat hitelesítési** az a menü. Majd állíthatja be a titkosítási kulcsot és a titkosítási paraméterek az alábbiak szerint:

    1. Hozzon létre egy vagy több titkosítási kulcsokat. A titkosítási kulcsot a kis-és nagybetűket, és az alfanumerikus karakterek tetszőleges kombinációját tartalmazhatja. Más típusú karaktereket, szóközöket is beleértve. nem engedélyezettek. A hossza legfeljebb 250 karakterből áll. Annak érdekében, hogy a titkosítási kulcsok véletlenszerű, ajánlott használatával létrehozni a [OpenSSL eszköz](https://www.openssl.org/). 

       A OpenSSL eszköz szintaxisa a következő:

       ```rand -hex <key length>```

       Példa:

       ```OpenSSL> rand -hex 32``` 

       Állásidő elkerülése érdekében hozzon létre egy elsődleges és a kulcsot egy biztonsági másolatból. Kulcsot egy biztonsági másolatból zavartalan hozzáférést biztosít azokhoz a tartalmat az elsődleges kulcs frissítésekor.
    
    2. Adjon meg egy egyedi titkosítási kulcsot a a **elsődleges kulcs** mezőbe, majd adja meg a kulcsot egy biztonsági másolatból a **biztonsági mentési kulcs** mezőbe.

    3. Válassza ki az egyes kulcsok minimális titkosítási verzióját a **minimális titkosítási verziója** listában, majd kattintson az **frissítés**:
       - **V2**: azt jelzi, hogy használható-e a kulcs 2.0 és 3.0 verziót jogkivonatok létrehozásához. Akkor használja ezt a beállítást, ha a 3.0-s verziója kulcs való váltás egy örökölt 2.0-s verziójának titkosítási kulcs.
       - **V3**: (ajánlott) azt jelzi, hogy a kulcsot csak használható 3.0-s verziója jogkivonatok létrehozásához.

    ![CDN-jogkivonat hitelesítési telepítési kulcs](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    4. A titkosítás eszközzel titkosítási paraméterek beállítása és a-token létrehozásához. A titkosítás eszközzel akkor engedélyezheti vagy tagadhatja meg kérelmet lejárati időt, ország, hivatkozó, protokoll és ügyfél IP-Címmel (tetszőleges kombinációját) alapján. Bár a szám és paramétereket, és egy tokent össze lehet kombinálni kombinációja korlátozva van, a teljes jogkivonat hossza legfeljebb 512 karakter hosszúságú lehet. 

       ![CDN eszköz titkosítása](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Adja meg az értékeket egy vagy több, a következő titkosítási paraméterek a **eszköz titkosítása** szakasz: 

       > [!div class="mx-tdCol2BreakAll"] 
       > <table>
       > <tr>
       >   <th>Paraméter neve</th> 
       >   <th>Leírás</th>
       > </tr>
       > <tr>
       >    <td><b>ec_expire</b></td>
       >    <td>Lejárati idő rendeli jogkivonatot, amely után a jogkivonat lejár. A rendszer megtagadja a lejárati idő után küldött kérelmeket. Ezt a paramétert használ egy Unix Timestamp értéket, a szabványos Unix epoch a másodpercek száma alapján `1/1/1970 00:00:00 GMT`. (Segítségével online eszközök téli idő és a Unix idő közötti átváltásra.)> 
       >    Például, ha azt szeretné, hogy az elévülés token `12/31/2016 12:00:00 GMT`, adja meg a Unix timestamp értéket `1483185600`. 
       > </tr>
       > <tr>
       >    <td><b>ec_url_allow</b></td> 
       >    <td>Lehetővé teszi testre szabni a tokenek egy adott eszköz vagy az elérési út. Hogy korlátozza a hozzáférést a kérések megadott relatív elérési úttal rendelkező start URL-cím. URL-címei kis-és nagybetűket. Több elérési utat adjon meg mindegyik elérési út; vesszővel elválasztva Ne adjon hozzá szóközöket. A követelményeitől függően állíthat be eltérő értékeket eltérő szintű hozzáférés biztosításához.> 
       >    Például, ha az URL-cím `http://www.mydomain.com/pictures/city/strasbourg.png`, ezek a kérelmek engedélyezettek a következő bemeneti értékeket: 
       >    <ul>
       >       <li>Adjon meg értéket `/`: minden kérelmek engedélyezettek.</li>
       >       <li>Adjon meg értéket `/pictures`, a következő kérelmek engedélyezettek: <ul>
       >          <li>`http://www.mydomain.com/pictures.png`</li>
       >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
       >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
       >       </ul></li>
       >       <li>Adjon meg értéket `/pictures/`: csak a tartalmazó kéri a `/pictures/` elérési út használata engedélyezett. Például: `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
       >       <li>Adjon meg értéket `/pictures/city/strasbourg.png`: a megadott elérési útra és az eszköz engedélyezett csak kéri.</li>
       >    </ul>
       > </tr>
       > <tr>
       >    <td><b>ec_country_allow</b></td> 
       >    <td>Csak lehetővé teszi, hogy egy vagy több megadott országokból kérelmekkel. Más országokból kérelmekkel a rendszer megtagadja. Használja a kétbetűs [ISO 3166 országhívószám](https://msdn.microsoft.com/library/mt761717.aspx) az egyes országok és mindegyiket egy-egy vesszővel külön; ne vegyen fel egy helyet. Ha szeretné engedélyezni a hozzáférést a csak az Egyesült Államokban és Franciaország, írja be például `US,FR`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_country_deny</b></td> 
       >    <td>Egy vagy több megadott országokból kérelmekkel megtagadja. Más országokból kérelmekkel engedélyezettek. A megvalósítás megegyezik a <b>ec_country_allow</b> paraméter. Ha az országkódot egyaránt megtalálható a <b>ec_country_allow</b> és <b>ec_country_deny</b> paraméterek, a <b>ec_country_allow</b> paraméter lép érvénybe.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_ref_allow</b></td>
       >    <td>A megadott hivatkozó kérések csak engedélyezi. A hivatkozó azonosítja a weblap, amely csatolva van a kért erőforrás URL-CÍMÉT. A paraméter értéke tartalmazza a protokollt.>    
       >    A bemenet a következő típusok használhatók:
       >    <ul>
       >       <li>Egy állomásnevet vagy egy állomásnevet és egy elérési utat.</li>
       >       <li>Több hivatkozó kérelmei. Több hivatkozó kérelmei hozzáadásához külön minden hivatkozó vesszővel; Ne adjon hozzá egy szóközzel. Ha hivatkozó értéket adjon meg, de a hivatkozó adatokat, az nem küldi el a kérést, mert a böngésző konfigurációs, a rendszer megtagadja a kérelmet, alapértelmezés szerint.</li> 
       >       <li>Kérelmek hiányzik vagy üres hivatkozó adatokkal. Alapértelmezés szerint a <b>ec_ref_allow</b> paraméter blokkolja az ilyen típusú kérelmeket. Ahhoz, hogy ezeket a kérelmeket, vagy a szöveget, "Hiányzó", vagy adjon meg egy üres értéket (záró vesszővel válassza el).</li> 
       >       <li>Altartományok. Altartományok engedélyezéséhez adja meg a csillag (\*). Ahhoz például, hogy engedélyezi az összes altartomány `contoso.com`, adja meg `*.contoso.com`.</li>
       >    </ul>     
       >    Ahhoz például, hogy engedélyezze a hozzáférést a kérelmeinek `www.contoso.com`, az összes altartomány `contoso2.com`, üres vagy hiányzó hivatkozó kérelmei rendelkező kérelmek esetében adja meg, és `www.contoso.com,*.contoso.com,missing`.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_ref_deny</b></td>
       >    <td>A megadott hivatkozó megtagadja. A megvalósítás megegyezik a <b>ec_ref_allow</b> paraméter. Ha egy hivatkozó egyaránt megtalálható a <b>ec_ref_allow</b> és <b>ec_ref_deny</b> paraméterek, a <b>ec_ref_allow</b> paraméter lép érvénybe.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_proto_allow</b></td> 
       >    <td>Csak lehetővé teszi, hogy a megadott protokoll érkező kérelmeket. Érvényes értékek a következők `http`, `https`, vagy `http,https`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_proto_deny</b></td>
       >    <td>A megadott protokoll megtagadja. A megvalósítás megegyezik a <b>ec_proto_allow</b> paraméter. Ha egy protokoll egyaránt megtalálható a <b>ec_proto_allow</b> és <b>ec_proto_deny</b> paraméterek, a <b>ec_proto_allow</b> paraméter lép érvénybe.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_clientip</b></td>
       >    <td>Korlátozza a hozzáférést a megadott kérelmező IP-címet. IPV4 és IPV6 használata támogatott. Egy kérelem egyetlen IP-címet vagy egy bizonyos alhálózat társított IP-címeket adhat meg. Például `11.22.33.0/22` lehetővé teszi a kérelmek 11.22.32.1 való 11.22.35.254 IP-címről.</td>
       > </tr>
       > </table>

    5. Miután befejezte a titkosítási paramétert értékek megadása, válasszon egy kulcs titkosításához (ha az elsődleges és a kulcsot egy biztonsági másolatból hozott létre) a **kulcs titkosításához** listája.
    
    6. Válassza ki a titkosítási verziójú a **titkosítási verziója** lista: **V2** 2-es verzió vagy **V3** verziójához 3 (ajánlott). 

    7. Kattintson a **titkosítása** a jogkivonat létrehozásához.

    A jogkivonat előállítása, után megjelenik a **generált jogkivonat** mezőbe. A token használatához hozzáfűzése lekérdezési karakterláncként az URL-címe a fájl végére. Például: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
    8. Lehetősége van ellenőrizni a jogkivonatot a visszafejtés eszközzel, így megtekintheti a token paraméterek. Illessze be a token értékét a **visszafejtése tokenjét** mezőbe. Válassza ki a titkosítási kulcsot használni a **kulcs visszafejtése** listában, majd kattintson az **visszafejtéséhez**.

    A token visszafejtése, miután a paraméterei megjelennek a **eredeti paraméterek** mezőbe.

    9. Másik lehetőségként testreszabása eredményül, ha a rendszer megtagadja a kérelmet válaszkód típusú. Válassza ki **engedélyezve**, majd válassza ki a válaszkódot a **válaszkód** listája. **Fejlécnév** automatikusan **hely**. Kattintson a **mentése** az új válaszkód végrehajtásához. Az egyes válaszkódot, meg kell adnia a a hibalap URL-CÍMÉT a **Fejlécérték** mezőbe. A **403** válaszának kódja (tiltott) alapértelmezettként van beállítva. 

3. A **HTTP nagy**, kattintson a **szabálymotor**. A szabályok motor használatával alkalmazza a szolgáltatás, a jogkivonat hitelesítési szolgáltatás engedélyezése és hitelesítésre vonatkozó további token képességek engedélyezése elérési utak megadása. További információkért lásd: [szabályok motor hivatkozás](cdn-rules-engine-reference.md).

    1. Jelöljön ki egy meglévő szabályt, vagy hozzon létre egy új szabályt, amely a token hitelesítést alkalmazni kívánt eszköz vagy elérési út megadása. 
    2. Egy szabály tokent használó hitelesítés engedélyezéséhez jelölje be  **[jogkivonat hitelesítési](cdn-rules-engine-reference-features.md#token-auth)**  a a **szolgáltatások** listában, majd válassza a **engedélyezve**. Kattintson a **frissítés** szabály frissítésekor vagy **Hozzáadás** szabály létrehozásakor.
        
    ![CDN szabályok motor tokent használó hitelesítés engedélyezése – példa](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. A szabályok motor további jogkivonat hitelesítési szolgáltatásokkal is engedélyezheti. A következő funkciók engedélyezéséhez válassza ki azt a **szolgáltatások** listában, majd válasszon **engedélyezve**.
    
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

Funkciókkal kapcsolatos információkért lásd: [CDN áttekintésével](cdn-overview.md). További információk a díjszabásról: [Content Delivery Network árképzési](https://azure.microsoft.com/pricing/details/cdn/).
