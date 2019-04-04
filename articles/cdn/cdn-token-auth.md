---
title: Az eszközjogkivonattal történő hitelesítés az Azure CDN-eszközök védelme |} A Microsoft Docs
description: Ismerje meg az eszközjogkivonattal történő hitelesítés használata az Azure CDN-eszközökhöz való hozzáférés biztonsága érdekében.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: 75d6fb063a6cb5336a4d9945bf6a79a65ed25d40
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918892"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Az eszközjogkivonattal történő hitelesítés az Azure CDN-eszközök védelme

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés

Jogkivonat-hitelesítés olyan mechanizmus, amely lehetővé teszi, hogy megakadályozza, hogy az Azure Content Delivery Network (CDN) szolgáló eszközök jogosulatlan ügyfelek számára. Jogkivonat-hitelesítés általában történik, hogy *hotlinking* tartalmának, amelyben egy másik webhelyre, egy üzenet táblához, például az eszközök engedély nélkül használja. Hotlinking hatással lehetnek a tartalomkézbesítési költségek. CDN-en tokent használó hitelesítés engedélyezésével kérelmek hitelesítése CDN peremhálózati kiszolgáló előtt a CDN a tartalmat továbbít. 

## <a name="how-it-works"></a>Működés

Jogkivonat-hitelesítés ellenőrzi, hogy kérelmeket alapján jönnek létre a megbízható helyek úgy, hogy visszatartással kódolású a kérelmező információ token értéket tartalmazó kérelmek. Tartalomkézbesítés egy kérelmezőnek csak akkor, ha a kódolt információt kielégíti; Ellenkező esetben kérelmek elutasítva. A követelmények beállítása egy vagy több, a következő paraméterek közül:

- Ország/régió: Engedélyezi vagy megtagadja a által meghatározott országokból kérelmekkel azok [országkód](/previous-versions/azure/mt761717(v=azure.100)).
- URL-cím: Csak azok a kérelmek, amelyek megfelelnek a megadott eszköz vagy az elérési út engedélyezése.
- Gazdagép: Engedélyezése vagy letiltása a kérelem fejlécében a megadott gazdagépek kérelmeket.
- Hivatkozó: Engedélyezi vagy elutasítja a kérelmet, a megadott hivatkozó.
- IP-cím: Csak a megadott IP-cím vagy IP-alhálózat származik kérések engedélyezése.
- Protokoll: Engedélyezi vagy megtagadja a kérelmet az alapján tartalomkérelem a használt protokollt.
- Lejárati idő: Rendeljen hozzá egy dátumot és az időszakot, győződjön meg arról, hogy a hivatkozás csak korlátozott ideig időszakra érvényes marad.

További információkért tekintse meg a részletes konfigurációs példák az egyes paramétereket lévő [alkalmazástoken-hitelesítésének beállítása](#setting-up-token-authentication).

>[!IMPORTANT] 
> Token használata engedélyezve van a bármilyen útvonalat ehhez a fiókhoz, standard-gyorsítótáras üzemmód-e a mód, amely a lekérdezési karakterláncok gyorsítótárazása is használható. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referenciaarchitektúra

A következő munkafolyamat ábra ismerteti, hogyan a CDN-t használja az eszközjogkivonattal történő hitelesítés a webalkalmazással együtt működik.

![A CDN-tokent használó hitelesítés biztosítását munkafolyamat](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Token ellenőrzési logika a CDN-végpont
    
A következő folyamatábra szemlélteti azt ismerteti, hogyan Azure CDN érvényesíti-e egy ügyfél kérelmet ha eszközjogkivonattal történő hitelesítés úgy van konfigurálva, a CDN-végponthoz.

![A CDN-jogkivonat ellenőrzési logika](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Jogkivonat-hitelesítés beállítása

1. Az a [az Azure portal](https://portal.azure.com), tallózással keresse meg a CDN-profilra, majd válassza ki **kezelés** a kiegészítő portál elindításához.

    ![A CDN-profil felügyelet gombra](./media/cdn-token-auth/cdn-manage-btn.png)

2. A kurzort **HTTP nagy**, majd **jogkivonat hitelesítési** a úszó menü a. Ezután állíthat be a titkosítási kulcsot és a titkosítási paraméterek a következő:

   1. Hozzon létre egy vagy több titkosítási kulcsokat. Titkosítási kulcs kis-és nagybetűket, és tetszőleges kombinációját alfanumerikus karaktereket tartalmazhat. Nem engedélyezett karaktereket, szóközöket, beleértve a más típusú. A hossza legfeljebb 250 karakter lehet. Győződjön meg arról, hogy a titkosítási kulcsok véletlenszerű, ajánlott a létrehozása, a [OpenSSL eszköz](https://www.openssl.org/). 

      Az OpenSSL eszköz szintaxisa a következő:

      ```rand -hex <key length>```

      Példa:

      ```OpenSSL> rand -hex 32``` 

      Állásidő elkerülése érdekében hozzon létre egy elsődleges és a kulcsot egy biztonsági másolatból. A biztonsági mentési kulcsának biztosít a megszakítás nélkül hozzáférhetnek a tartalmaihoz, amikor az elsődleges kulcs frissítése folyamatban van.
    
   2. Adjon meg egy egyedi titkosítási kulcsot a a **elsődleges kulcs** mezőbe, és szükség esetén adja meg a kulcsot egy biztonsági másolatból a **biztonsági mentési kulcsának** mezőbe.

   3. Válassza ki a minimális titkosítási verziót minden egyes kulcshoz az annak **titkosítási minimális verzió** listában, majd válassza ki **frissítés**:
      - **V2**: Azt jelzi, hogy használható-e a kulcs 2.0 és 3.0 verziót jogkivonatokat hoz létre. Akkor használja ezt a beállítást, ha a 3.0-s verzió kulcsok transitioning 2.0-s verziójának régi titkosítási kulcs.
      - **V3**: (Ajánlott) Azt jelzi, hogy a kulcs csak használható a 3.0-s verzió jogkivonatokat hoz létre.

      ![CDN jogkivonat hitelesítési telepítési kulcs](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. A titkosítás eszközzel titkosítási paraméterek beállítása és a egy jogkivonatot. A titkosítás eszközzel engedélyezheti vagy megtagadja a kérelmet lejárati idő, az ország, a hivatkozó, a protokoll és a ügyfél IP-Címét (a tetszőleges kombinációjának) alapján. Bár nincs korlátozva a számát és kombinációja, amely az űrlap-token paraméterek, a teljes jogkivonat hossza legfeljebb 512 karakter hosszúságú lehet. 

      ![CDN eszköz titkosítása](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Adja meg az értékeket a következő titkosítási paraméterek közül legalább egyet a **eszköz titkosítása** szakaszban: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Paraméter neve</th> 
      >   <th>Leírás</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Lejárati idő rendel egy jogkivonatot, amely után a jogkivonat lejár. Miután a rendszer megtagadta a lejárati időt a elküldött kérelmek. Ezt a paramétert használ egy Unix timestamp, amely alapul, a standard szintű Unix alapidőpont óta eltelt másodpercek számát `1/1/1970 00:00:00 GMT`. (Segítségével online eszközök közötti téli idő- és Unix-ideje.)> 
      >    Például, ha azt szeretné, hogy a jogkivonat lejár `12/31/2016 12:00:00 GMT`, adja meg a Unix-időbélyegző értékét `1483185600`. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Egy adott eszköz vagy az elérési út jogkivonatok megfelelő kódolási sebesség kiválasztását teszi lehetővé. Korlátozza a hozzáférést a kérelmekre, amelynek URL-hivatkozásának eleje meghatározott relatív elérési utat. URL-címek olyan kis-és nagybetűket. Adjon meg több elérési út minden elérési útja; vesszővel elválasztva ne vegye fel a szóközöket. A követelményeitől függően állíthat be eltérő értékeket különböző szintű hozzáférést biztosít.> 
      >    Például, ha az URL-cím `http://www.mydomain.com/pictures/city/strasbourg.png`, ezek a kérelmek engedélyezettek a következő értékeket: 
      >    <ul>
      >       <li>Bemeneti érték `/`: Az összes kérelmek engedélyezettek.</li>
      >       <li>Bemeneti érték `/pictures`, a következő kérelmek engedélyezettek: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Bemeneti érték `/pictures/`: Csak a tartalmazó kérelmek a `/pictures/` elérési út használata engedélyezett. Például: `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Bemeneti érték `/pictures/city/strasbourg.png`: Ez a megadott elérési út és az eszköz csak kérelmek engedélyezettek.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Csak lehetővé teszi, hogy egy vagy több megadott országokból kérelmekkel. A rendszer megtagadta a más országokból kérelmekkel. Használja a kétbetűs [ISO 3166 országkód](/previous-versions/azure/mt761717(v=azure.100)) országokra, és mindegyiket egy-egy vesszővel tagolt külön; ne vegyen fel egy helyet. Ha azt szeretné, engedélyezze a hozzáférést csak az Egyesült Államokban és Franciaország, írja be például `US,FR`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Egy vagy több megadott országokból kérelmekkel megtagadja. Más országokból kérelmekkel engedélyezettek. A megvalósítás pedig ugyanaz, mint a <b>ec_country_allow</b> paraméter. Ha az országkódot is megtalálható a <b>ec_country_allow</b> és <b>ec_country_deny</b> paraméterek, a <b>ec_country_allow</b> paraméter élvez elsőbbséget.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Csak a megadott hivatkozó származó kérelmek teszi lehetővé. A hivatkozó azonosítja, amely kapcsolódik az erőforrás kért weblap URL-CÍMÉT. A paraméter értéke nem tartalmazzák a protokollt.> 
      >    A következő típusú bemeneti engedélyezettek:
      >    <ul>
      >       <li>Egy állomásnevet vagy egy állomásnevet és egy elérési utat.</li>
      >       <li>Több hivatkozók. Adjon hozzá több hivatkozók, minden egyes hivatkozó külön vesszővel válasszon el; Ne adjon hozzá egy szóközt. Ha megad hivatkozó, de a hivatkozó információt rendszer nem küldi el a kérést, mert a webböngésző konfigurációját, a rendszer megtagadja a kérelmet, alapértelmezés szerint.</li> 
      >       <li>Kérelmek száma hiányzik vagy üres hivatkozó adatokkal. Alapértelmezés szerint a <b>ec_ref_allow</b> paraméter blokkolja az ilyen típusú kérelmeket. Ahhoz, hogy ezeket a kérelmeket, vagy a szöveget, "Hiányzó", vagy adjon meg egy üres értéket (a záró vessző használatával).</li> 
      >       <li>Subdomains. Ahhoz, hogy altartományt, írjon be egy csillagot (\*). Például, hogy az összes altartományainak `contoso.com`, adja meg `*.contoso.com`.</li>
      >    </ul> 
      >    Például, hogy hozzáférési kéréseit `www.contoso.com`, altartományokkal alatt `contoso2.com`, és adja meg a kérések hivatkozók üres vagy hiányzik, a `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>A megadott hivatkozó a megtagadja. A megvalósítás pedig ugyanaz, mint a <b>ec_ref_allow</b> paraméter. Ha egy hivatkozó is megtalálható a <b>ec_ref_allow</b> és <b>ec_ref_deny</b> paraméterek, a <b>ec_ref_allow</b> paraméter élvez elsőbbséget.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Csak lehetővé teszi, hogy a megadott protokoll érkező kérelmeket. Érvényes értékek a következők `http`, `https`, vagy `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>A megadott protokoll megtagadja. A megvalósítás pedig ugyanaz, mint a <b>ec_proto_allow</b> paraméter. Ha egy protokollt is megtalálható a <b>ec_proto_allow</b> és <b>ec_proto_deny</b> paraméterek, a <b>ec_proto_allow</b> paraméter élvez elsőbbséget.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Korlátozza a hozzáférést a megadott kérelmező IP-címet. IPV4 és IPv6-alapú egyaránt támogatottak. Egyetlen kérelem IP-cím vagy a megadott alhálózat társított IP-címek is megadhat. Ha például `11.22.33.0/22` lehetővé teszi a kérelmek 11.22.32.1 való 11.22.35.254 IP-címekről.</td>
      > </tr>
      > </table>

   5. Miután befejezte a titkosítási paraméterértékek megadása, válasszon ki egy kulcsot titkosítása (ha az elsődleges és a egy biztonsági mentési kulcsának létrehozott) a **kulcs titkosítására** listája.
    
   6. Válasszon egy titkosítási verziót a **titkosítási verzió** lista: **V2** 2-es verzióját vagy **V3** a (javasolt) 3-as verziójú. 

   7. Válassza ki **titkosítása** a token létrehozásához.

      Miután a jogkivonat jön létre, akkor az megjelenik a **generált jogkivonat** mezőbe. Használja a jogkivonatot, fűzze hozzá az URL-cím a fájl végéhez lekérdezési karakterláncként. Például: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. A token a visszafejtési eszközzel lehetősége van ellenőrizni, hogy a token paraméterek is megtekintheti. Illessze be a token értékét a **Token visszafejtésére** mezőbe. Válassza ki a titkosítási kulcs használatát a **kulcs visszafejtéséhez** listában, majd válassza ki **visszafejtéséhez**.

      A jogkivonat visszafejtése, miután a paraméterek megjelennek a **eredeti paraméterek** mezőbe.

   9. Igény szerint testre szabhatja a válaszkód, amikor a rendszer megtagadja a kérelmet visszaadott típusát. Válassza ki **engedélyezve**, majd válassza ki a válaszkódot a **válaszkód** listája. **Fejléc neve** értéke automatikusan **hely**. Válassza ki **mentése** az új válasz kód megvalósítását. Az egyes válaszkódot, meg kell adnia az URL-címét a hibalaphoz a **Fejlécérték** mezőbe. A **403-as** válaszkód (tiltott) alapértelmezés szerint ki van választva. 

3. A **HTTP nagy**válassza **Szabálymotorral**. A rules engine használatával határozza meg az elérési utak a alkalmazni a funkció, a jogkivonat-hitelesítés engedélyezéséhez és további jogkivonat hitelesítési képességek engedélyezéséhez. További információkért lásd: [szabálymotor-referencia](cdn-rules-engine-reference.md).

   1. Jelöljön ki egy meglévő szabályt, vagy hozzon létre egy új szabályt, amelynek a tokent használó hitelesítés biztosítását a alkalmazni szeretné az eszközintelligencia vagy elérési útja. 
   2. A szabályok jogkivonat-hitelesítés engedélyezéséhez jelölje be **[jogkivonat hitelesítési](cdn-rules-engine-reference-features.md#token-auth)** a a **funkciók** listában, majd válassza ki **engedélyezve**. Válassza ki **frissítés** szabály frissítésekor vagy **Hozzáadás** szabály létrehozásakor.
        
      ![CDN szabálymotor eszközjogkivonattal történő hitelesítés engedélyezése példa](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. A rules engine további jogkivonat hitelesítési szolgáltatások is engedélyezheti. A következő funkciók engedélyezéséhez válassza ki a **funkciók** listában, majd válassza ki **engedélyezve**.
    
   - **[Jogkivonat-hitelesítés megtagadása kód](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: Amikor a rendszer megtagadja a kérelmet a felhasználó visszaadott válaszának típusa határozza meg. Itt beállított szabályok felülbírálják a válaszkód, állítsa be a **egyéni elutasítási kezelése** szakasz a hitelesítési jogkivonat-alapú lapon.

   - **[Jogkivonat hitelesítési URL-cím egyezőek](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Meghatározza, hogy a jogkivonat érvényesítéséhez használt URL-cím kis-és nagybetűket.

   - **[Jogkivonat-hitelesítés paraméter](cdn-rules-engine-reference-features.md#token-auth-parameter)**: Átnevezi a hitelesítési jogkivonat lekérdezésisztring-paraméter, amely a kért URL-cím jelenik meg. 
        
     ![CDN szabálymotor jogkivonat-hitelesítés beállításai példa](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Testre szabható a jogkivonatot a forráskód [GitHub](https://github.com/VerizonDigital/ectoken).
   Elérhető nyelvek:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Az Azure CDN szolgáltatásai és a szolgáltató díjszabása

Szolgáltatásokkal kapcsolatos információkért lásd: [Azure CDN termékszolgáltatásai](cdn-features.md). Díjszabással kapcsolatos információkért lásd: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).
