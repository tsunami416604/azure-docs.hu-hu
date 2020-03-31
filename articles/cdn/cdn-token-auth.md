---
title: Az Azure CDN-eszközök védelme tokenhitelesítéssel| Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a token hitelesítést az Azure CDN-eszközökhöz való biztonságos hozzáféréshez.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: fa71f472294b91baebc2a6075ddb2b50123e545d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593397"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Az Azure CDN-eszközök védelme tokenhitelesítéssel

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés

A tokenhitelesítés egy olyan mechanizmus, amely lehetővé teszi, hogy megakadályozza, hogy az Azure Content Delivery Network (CDN) nem engedélyezett ügyfelek számára szolgáljon ki eszközöket. A tokenhitelesítés általában a tartalom *hotlinkingjének* megakadályozására történik, amelyben egy másik webhely, például egy üzenőfal engedély nélkül használja az eszközöket. A hotlinking hatással lehet a tartalomkézbesítési költségekre. A cdn-alapú tokenhitelesítés engedélyezésével a cdn peremhálózati kiszolgáló jataszja a kérelmeket, mielőtt a CDN kézbesítené a tartalmat. 

## <a name="how-it-works"></a>Működés

A tokenhitelesítés ellenőrzi, hogy a kérelmeket egy megbízható hely hozza-e létre, mivel megköveteli, hogy a kérelmek olyan jogkivonat-értéket tartalmazzanak, amely a kérelmező kódolt adatait tartalmazza. A tartalmat csak akkor szolgálják ki a kérelmezőnek, ha a kódolt információ megfelel a követelményeknek; ellenkező esetben a kérelmeket a kéréseket a felhasználók elutasítják. A követelményeket az alábbi paraméterek egyikével vagy többel állíthatja be:

- Ország: Az [országkódjukban](/previous-versions/azure/mt761717(v=azure.100))meghatározott országokból/régiókból származó kérelmek engedélyezése vagy megtagadása .
- URL:Csak a megadott eszköznek vagy elérési útnak megfelelő kérelmek engedélyezése.
- Állomás: A kérelem fejlécében a megadott állomásokat használó kérelmek engedélyezése vagy megtagadása.
- Hivatkozó: Kérés engedélyezése vagy megtagadása a megadott hivatkozótól.
- IP-cím: Csak adott IP-címről vagy IP-alhálózatról származó kérelmek engedélyezése.
- Protokoll: A tartalom kéréséhez használt protokollon alapuló kérelmek engedélyezése vagy megtagadása.
- Lejárati idő: Rendeljen hozzá egy dátumot és időszakot annak érdekében, hogy a hivatkozás csak korlátozott ideig maradjon érvényes.

További információt a tokenhitelesítés beállítása című témakörben talál az egyes paraméterek részletes konfigurációs [példáiban.](#setting-up-token-authentication)

>[!IMPORTANT] 
> Ha a fiók bármely elérési útjára engedélyezve van a tokenhitelesítés, a szabványos gyorsítótárazási mód az egyetlen mód, amely használható a lekérdezési karakterlánc-gyorsítótárazáshoz. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referenciaarchitektúra

Az alábbi munkafolyamat-diagram azt ismerteti, hogy a CDN hogyan használja a tokenhitelesítést a webalkalmazással való együttműködésre.

![CDN tokenhitelesítési munkafolyamat](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Token-érvényesítési logika a CDN-végponton
    
Az alábbi folyamatábra azt ismerteti, hogy az Azure CDN hogyan érvényesíti az ügyfélkérelmet, ha a CDN-végponton jogkivonat-hitelesítés van konfigurálva.

![CDN-token érvényesítési logikája](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Tokenhitelesítés beállítása

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a CDN-profilját, majd válassza **a Kezelés** lehetőséget a kiegészítő portál elindításához.

    ![CDN-profil kezelés gombja](./media/cdn-token-auth/cdn-manage-btn.png)

2. Mutasson a **HTTP Large**elemre, majd válassza a **Token Auth** elemet az úszó panelen. Ezután a titkosítási kulcsot és a titkosítási paramétereket a következőképpen állíthatja be:

   1. Hozzon létre egy vagy több titkosítási kulcsot. A titkosítási kulcs a kis- és nagybetűket is figyelembe veheti, és alfanumerikus karakterek tetszőleges kombinációját tartalmazhatja. Más típusú karakterek, beleértve a szóközöket is, nem engedélyezettek. A maximális hossz 250 karakter. Annak érdekében, hogy a titkosítási kulcsok véletlenszerűek legyenek, ajánlott létrehozni őket az [OpenSSL eszközzel.](https://www.openssl.org/) 

      Az OpenSSL eszköz szintaxisa a következő:

      ```rand -hex <key length>```

      Példa:

      ```OpenSSL> rand -hex 32``` 

      Az állásidő elkerülése érdekében hozzon létre egy elsődleges és egy biztonsági másolat kulcsot is. A biztonságimásolat-kulcs megszakítás nélküli hozzáférést biztosít a tartalomhoz az elsődleges kulcs frissítésekor.
    
   2. Írjon be egy egyedi titkosítási kulcsot az **Elsődleges kulcs** mezőbe, és tetszés szerint adjon meg egy biztonsági másolat kulcsot a Biztonsági **másolat kulcsa** mezőbe.

   3. Válassza ki az egyes kulcsok minimális titkosítási verzióját a **Minimális titkosítási verzió listából,** majd válassza a **Frissítés**lehetőséget:
      - **V2**: Azt jelzi, hogy a kulcs a 2.0-s és 3.0-s verziójú tokenek létrehozásához használható. Csak akkor használja ezt a beállítást, ha örökölt 2.0-s verziójú titkosítási kulcsról 3.0-s verziójú kulcsra vált.
      - **V3**: (Ajánlott) Azt jelzi, hogy a kulcs csak 3.0-s verziójú tokenek létrehozásához használható.

      ![CDN token hitelesítési kulcsa](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. A titkosítási eszköz segítségével titkosítási paramétereket állíthat be, és hozzon létre egy jogkivonatot. A titkosító eszközzel engedélyezheti vagy megtagadhatja a kérelmeket a lejárati idő, az ország/régió, a hivatkozó, a protokoll és az ügyfél IP-címe alapján (bármilyen kombinációban). Bár nincs korlátozva a tokenként egyesíthető paraméterek száma és kombinációja, a jogkivonat teljes hossza 512 karakterre korlátozódik. 

      ![CDN titkosító eszköz](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Adja meg az alábbi titkosítási paraméterek egy vagy több értékét a **Titkosítás eszköz** szakaszban: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Paraméter neve</th> 
      >   <th>Leírás</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Lejárati időt rendel egy jogkivonathoz, amely után a jogkivonat lejár. A lejárati idő után küldött kérelmek et a lejárati idő megtagadva. Ez a paraméter Unix időbélyeget használ, amely a szokásos Unix-korszak óta eltelt másodpercek `1/1/1970 00:00:00 GMT`számán alapul. (Az online eszközökkel konvertálhat a téli idő és a Unix idő között.)> 
      >    Ha például azt szeretné, hogy `12/31/2016 12:00:00 GMT`a token a , `1483185600`írja be a Unix időbélyeg értékét, írja be a . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Lehetővé teszi, hogy a jogkivonatokat egy adott eszközhöz vagy elérési úthoz igazítsa. Korlátozza a hozzáférést azokhoz a kérelmekhez, amelyek URL-címe egy adott relatív elérési úttal kezdődik. Az URL-címek ben a kis- és nagybetűket nem lehet figyelembe. Több görbe bevitele az egyes görbék vesszővel való elválasztásával; ne adjon szóközt. A követelményektől függően különböző értékeket állíthat be, hogy különböző szintű hozzáférést biztosítson.> 
      >    Az URL-cím `http://www.mydomain.com/pictures/city/strasbourg.png`esetében például ezek a kérések a következő bemeneti értékekre engedélyezettek: 
      >    <ul>
      >       <li>Bemeneti `/`érték : Minden kérelem engedélyezett.</li>
      >       <li>Bemeneti `/pictures`érték , a következő kérelmek engedélyezettek: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Bemeneti `/pictures/`érték: Csak `/pictures/` az elérési utat tartalmazó kérelmek engedélyezettek. Például: `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Bemeneti `/pictures/city/strasbourg.png`érték: Csak az adott elérési úthoz és eszközhöz tartozó kérelmek engedélyezettek.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Csak egy vagy több meghatározott országból/régióból származó kérelmeket engedélyezi. Az összes többi országból/régióból származó kérelmeket a rendszer elutasítja. Minden országhoz használjon kétbetűs [ISO 3166 országkódot,](/previous-versions/azure/mt761717(v=azure.100)) és mindegyikről vesszővel váljon el; ne adjon helyet. Ha például csak az Egyesült Államokból és Franciaországból `US,FR`szeretne hozzáférést engedélyezni, írja be a be parancsot.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Egy vagy több meghatározott országból/régióból származó kérelmek lemondja. Az összes többi országból/régióból származó kérelmek engedélyezettek. A megvalósítás megegyezik a <b>ec_country_allow</b> paraméterrel. Ha egy országkód mind a <b>ec_country_allow,</b> mind <b>a ec_country_deny</b> paraméterben jelen van, a <b>ec_country_allow</b> paraméter élvez elsőbbséget.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Csak a megadott hivatkozótól érkező kéréseket engedélyezi. A hivatkozó azonosítja a kért erőforráshoz kapcsolódó weblap URL-címét. Ne adja bele a protokollt a paraméterértékbe.> 
      >    A következő típusú bevitel engedélyezett:
      >    <ul>
      >       <li>Állomásnév vagy állomásnév és elérési út.</li>
      >       <li>Több ajánló. Több hivatkozó hozzáadásához különítse el az egyes hivatkozókat vesszővel; ne adjon helyet. Ha megad egy hivatkozói értéket, de a böngésző konfigurációja miatt a rendszer nem küldi el a hivatkozói adatokat a kérelemben, a rendszer alapértelmezés szerint elutasítja a kérést.</li> 
      >       <li>Hiányzó vagy üres hivatkozói adatokkal rendelkező kérések. Alapértelmezés szerint a <b>ec_ref_allow</b> paraméter blokkolja az ilyen típusú kérelmeket. A kérelmek engedélyezéséhez írja be a "hiányzó" szöveget, vagy írjon be egy üres értéket (záró vesszővel).</li> 
      >       <li>Aldomain. Az altartományok engedélyezéséhez írjon be\*egy csillagot ( ). Ha például engedélyezni szeretné a( a) altartományok összes altartományát, `contoso.com`írja be a be írását. `*.contoso.com`</li>
      >    </ul> 
      >    Ha például engedélyezni szeretné `www.contoso.com`a hozzáférést a `contoso2.com`rendszerből érkező kérelmekhez, a alatt `www.contoso.com,*.contoso.com,missing`található összes altartományhoz és az üres vagy hiányzó hivatkozókkal rendelkező kérésekhez, írja be a be.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>A megadott hivatkozó tól érkező kérések letagadja. A megvalósítás megegyezik a <b>ec_ref_allow</b> paraméterrel. Ha egy hivatkozó mind a <b>ec_ref_allow,</b> mind <b>a ec_ref_deny</b> paraméterben jelen van, a <b>ec_ref_allow</b> paraméter élvez elsőbbséget.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Csak a megadott protokolltól érkező kéréseket engedélyezi. Az érvényes `http` `https`értékek `http,https`a , vagy a .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>A megadott protokollkérelmeinek letagadja. A megvalósítás megegyezik a <b>ec_proto_allow</b> paraméterrel. Ha egy protokoll mind a <b>ec_proto_allow,</b> mind <b>a ec_proto_deny</b> paraméterben jelen van, a <b>ec_proto_allow</b> paraméter élvez elsőbbséget.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Korlátozza a hozzáférést a megadott kérelmező IP-címéhez. Mind az IPV4, mind az IPV6 támogatott. Megadhat egyetlen kérelem IP-címét vagy egy adott alhálózathoz társított IP-címeket. Például `11.22.33.0/22` engedélyezi a 11.22.32.1 és 11.22.35.254 IP-címekről érkező kérelmeket.</td>
      > </tr>
      > </table>

   5. Miután befejezte a titkosítási paraméterértékek beírását, jelöljön ki egy titkosítandó kulcsot (ha létrehozott egy elsődleges és egy biztonsági másolat kulcsot is) a **Kulcs titkosításhoz** listából.
    
   6. Válasszon egy titkosítási verziót a **Titkosítási verzió** listából: **V2** a 2-es verzióhoz vagy **V3** a 3-as verzióhoz (ajánlott). 

   7. Válassza **a Titkosítás lehetőséget** a jogkivonat létrehozásához.

      A jogkivonat létrehozása után megjelenik a **Generált jogkivonat** mezőben. A token használatához fűzze hozzá lekérdezési karakterláncként az URL-cím elérési útján lévő fájl végéhez. Például: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Szükség esetén tesztelje a jogkivonatot a visszafejtő eszközzel, hogy megtekinthesse a token paramétereit. Illessze be a token értékét a **visszafejtéshez** szükséges jogkivonat mezőbe. Jelölje ki a **visszafejthető kulcs** listában a használandó titkosítási kulcsot, majd válassza **a Visszafejtés**lehetőséget.

      A token visszafejtése után a paraméterek megjelennek az **Eredeti paraméterek** mezőben.

   9. Szükség esetén testreszabhatja a kérés megtagadása esetén visszaadott válaszkód típusát. Válassza **az Engedélyezve**lehetőséget, majd válassza ki a válaszkódot a **Válaszkód** listából. **A fejlécneve** automatikusan **Hely**lesz állítva. Az új válaszkód megvalósításához válassza a **Mentés** lehetőséget. Bizonyos válaszkódok esetén a hibaoldal URL-címét is meg kell adnia a **Fejlécérték** mezőben. A **403-as** válaszkód (Tiltott) alapértelmezés szerint be van jelölve. 

3. A **HTTP Large csoportban**válassza **a Szabálykezelő motor lehetőséget.** A szabálymotor segítségével útvonalakat definiálhat a szolgáltatás alkalmazásához, engedélyezheti a tokenhitelesítési szolgáltatást, és további jogkivonat-hitelesítéssel kapcsolatos képességeket engedélyezhet. További információ: [Rules engine reference](cdn-rules-engine-reference.md).

   1. Jelöljön ki egy meglévő szabályt, vagy hozzon létre egy új szabályt, amely meghatározza azt az eszközt vagy elérési utat, amelyre a tokenhitelesítést alkalmazni szeretné. 
   2. Ha engedélyezni szeretné a tokenhitelesítést egy szabályon, válassza a **[Token Auth](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)** elemet a **Szolgáltatások** listában, majd válassza az **Engedélyezve**lehetőséget. Válassza a **Frissítés** lehetőséget, ha egy szabályt frissít, vagy a **Hozzáadás** lehetőséget, ha szabályt hoz létre.
        
      ![CDN-szabályok motortoken-hitelesítése példát engedélyez](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. A szabálymotorban további jogkivonat-hitelesítéssel kapcsolatos funkciókat is engedélyezhet. Az alábbi szolgáltatások bármelyikének engedélyezéséhez jelölje ki azt a **Szolgáltatások** listából, majd válassza az **Engedélyezve**lehetőséget.
    
   - **[Token hitelesítési megtagadási kód:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)** Meghatározza, hogy milyen típusú választ ad vissza a felhasználónak, ha egy kérés megtagadva. Az itt beállított szabályok felülbírálják a jogkivonat-alapú hitelesítési lap **Egyéni megtagadáskezelés** szakaszában beállított válaszkódot.

   - **[Token hitelesítése URL-eset figyelmen kívül hagyása:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)** Azt határozza meg, hogy a jogkivonat érvényesítéséhez használt URL-cím nem-e kis- és nagybetűket.

   - **[Token Auth paraméter:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)** Átnevezi a token hitelesítési lekérdezési karakterlánc-paraméter, amely megjelenik a kért URL-cím. 
        
     ![Példa a CDN-szabályok motortoken-hitelesítési beállításaira](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Testreszabhatja a jogkivonatot a [GitHub](https://github.com/VerizonDigital/ectoken)forráskódjának elérésével.
   A rendelkezésre álló nyelvek a következők:
    
   - C#
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Az Azure CDN-funkciók és a szolgáltató díjszabása

A szolgáltatásokról az [Azure CDN termékszolgáltatásai](cdn-features.md)című témakörben talál további információt. Az árképzésről a [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/)című témakörben talál további információt.
