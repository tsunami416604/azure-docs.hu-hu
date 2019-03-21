---
title: SAS használatával Azure CDN szolgáltatás használata |} A Microsoft Docs
description: Az Azure CDN támogatja a közös hozzáférésű Jogosultságkód (SAS) korlátozott hozzáférést privát storage-tárolók használatát.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.openlocfilehash: ee64b4cbfd024c91b226736bc8cac0b9b33f964e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170394"
---
# <a name="using-azure-cdn-with-sas"></a>SAS használatával Azure CDN szolgáltatás használata

Beállításakor egy storage-fiókot az Azure tartalom Delivery Network (CDN) használatához a tartalmak gyorsítótárazására alapértelmezés szerint bárki, aki ismeri a storage-tárolók URL-hozzáférhet az Ön által feltöltött fájlokat. A storage-fiókban a fájlok védelméhez, állíthatja be a hozzáférést, a storage-tárolók a nyilvános, privát. Azonban ha így tesz, senki nem fogja tudni elérni a fájlokat. 

Ha korlátozott hozzáférést szeretne adni privát tárolókhoz, használhatja Azure-tárfiókjának közös hozzáférésű jogosultságkód (SAS) funkcióját. A SAS olyan URI, amely az Azure Storage erőforrásainak korlátozott hozzáférési jogosultságát biztosítja anélkül, hogy közzétenné a fiók kulcsát. Megadhat egy SAS nem bízik meg a tárfiók kulcsának az ügyfeleken, de szeretne egyes storage-fiók erőforrásokhoz való hozzáférés delegálására. Közös hozzáférésű jogosultságkód URI ezen ügyfeleknek való elosztásával hozzáférést biztosít számukra egy erőforráshoz egy megadott ideig.
 
A SAS használatával megadhat paramétereket, amelyek különböző hozzáférés egy blobba, például a kezdő és lejárati idejét, engedéllyel (olvasás/írás) és IP-címtartományok. Ez a cikk ismerteti az Azure CDN együttes SAS használatával. SAS, beleértve a hogyan hozhat létre, és a paraméterbeállításokkal kapcsolatos további részletekért lásd: [a közös hozzáférésű jogosultságkód (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Az storage SAS használatához az Azure CDN beállítása
A következő három módon SAS használatával az Azure CDN használata javasolt. Minden beállítás azt feltételezik, hogy már létrehozott egy működő SAS (lásd az Előfeltételek). 
 
### <a name="prerequisites"></a>Előfeltételek
Először hozzon létre egy tárfiókot, és ezután hozza létre az eszközhöz tartozó SAS. Tárolt hozzáférési aláírások két típusú is létrehozhat: a szolgáltatásalapú SAS vagy egy SAS-fiókot. További információkért lásd: [közös hozzáférésű jogosultságkódok típusú](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

SAS-token létrehozását követően érheti el a blob storage-fájl hozzáfűzése `?sv=<SAS token>` az URL-címre. Az URL-cím formátuma a következő: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Példa:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Beállítás-paraméterekkel kapcsolatos további információkért lásd: [SAS paraméter szempontok](#sas-parameter-considerations) és [közös hozzáférési aláírási paraméterek](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![CDN SAS-beállítások](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Option 1: SAS használata a csatlakoztatott a blob storage-bA az Azure CDN szolgáltatással

Ez a beállítás a legegyszerűbben, és a egy SAS-jogkivonatát, amelyet az Azure CDN a forráskiszolgáló használja.
 
1. Válassza ki a végpont kiválasztása **gyorsítótár-szabályokkal**, majd **minden egyedi URL gyorsítótárazása** a a **lekérdezési karakterláncok gyorsítótárazása** lista.

    ![CDN gyorsítótárazási szabályok](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. SAS a tárfiókon beállította az CDN endpoint és a forrás URL-a SAS-jogkivonat kell használnia a fájl elérésére. 
   
   Az eredményül kapott CDN végponti URL-cím formátuma a következő: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Példa:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. A gyorsítótárazás időtartama finomhangolása gyorsítótárazási szabályok segítségével, vagy a hozzáadásával `Cache-Control` fejlécek a forrás-kiszolgálón. Mivel az Azure CDN a SAS-jogkivonat kezeli az egyszerű lekérdezési karakterláncként, ajánlott eljárásként érdemes beállítania a gyorsítótárazás időtartamát, vagy az előtt a SAS-lejárati idő lejár. Ellenkező esetben, ha egy fájlt a rendszer több, mint a biztonsági Társítások aktív hosszabb időre gyorsítótárazza, a fájl valószínűleg elérhető az Azure CDN origin kiszolgálóról a SAS-lejárati idő eltelte után. Ha ez a helyzet akkor fordul elő, és azt szeretné, hogy a gyorsítótárazott fájl nem érhető el, törölje a jelölést a gyorsítótárból a fájl egy kiürítési műveletet kell végrehajtania. Az Azure CDN a gyorsítótárazás idejének beállításával kapcsolatos további információkért lásd: [vezérlő Azure CDN gyorsítótárazási viselkedésének gyorsítótár-szabályokkal](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Option 2: Rejtett CDN SAS-jogkivonat-átírási szabály használatával
 
Ez a beállítás csak érhető el **verizon Azure CDN Premium** profilok. Ezzel a beállítással gondoskodhat a blob-tárolóból az eredeti kiszolgálóra. Előfordulhat, hogy szeretné használja ezt a beállítást, ha nem szükséges különleges hozzáférési korlátozásokat a fájlt, de szeretné akadályozni, hogy a felhasználók közvetlenül az Azure CDN kiszervezési idők javítása érdekében a tároló forrás eléréséhez. A SAS-jogkivonat, amely a felhasználó ismeretlen, bárki fájlokat az eredeti kiszolgálóra a blobtároló eléréséhez szükség. Azonban az URL-Újraírási szabályt, mert a SAS-jogkivonat nem szükséges a CDN-végponton.
 
1. Használja a [szabálymotorral](cdn-rules-engine.md) URL-újraíró szabály létrehozásához. Új szabályok propagálása akár 4 óra múlva.

   ![CDN kezelése gomb](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN szabálymotor gomb](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Az alábbi minta URL-újraíró szabály egy reguláris kifejezési mintája rögzítését csoport és a egy nevű végpontot használ *sasstoragedemo*:
   
   Forrás:   
   `(container1\/.*)`
   
   Cél:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![A CDN URL-újraíró szabály – bal](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL-újraíró szabály – jobbra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Miután az új szabály aktiválódásakor, bárki hozzáférhet a CDN-végpont, függetlenül attól, hogy használatát egy SAS-token az URL-címben lévő fájlok a megadott tároló. A következő formátumban: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Példa:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. A gyorsítótárazás időtartama finomhangolása gyorsítótárazási szabályok segítségével, vagy a hozzáadásával `Cache-Control` fejlécek a forrás-kiszolgálón. Mivel az Azure CDN a SAS-jogkivonat kezeli az egyszerű lekérdezési karakterláncként, ajánlott eljárásként érdemes beállítania a gyorsítótárazás időtartamát, vagy az előtt a SAS-lejárati idő lejár. Ellenkező esetben, ha egy fájlt a rendszer több, mint a biztonsági Társítások aktív hosszabb időre gyorsítótárazza, a fájl valószínűleg elérhető az Azure CDN origin kiszolgálóról a SAS-lejárati idő eltelte után. Ha ez a helyzet akkor fordul elő, és azt szeretné, hogy a gyorsítótárazott fájl nem érhető el, törölje a jelölést a gyorsítótárból a fájl egy kiürítési műveletet kell végrehajtania. Az Azure CDN a gyorsítótárazás idejének beállításával kapcsolatos további információkért lásd: [vezérlő Azure CDN gyorsítótárazási viselkedésének gyorsítótár-szabályokkal](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>3. lehetőség: CDN biztonsági jogkivonat-hitelesítés használata egy újraírási szabályt

Az Azure CDN biztonsági jogkivonat-hitelesítés használatához rendelkeznie kell egy **verizon Azure CDN Premium** profilt. Ez a beállítás akkor a legbiztonságosabb és testre szabható. Ügyfél-hozzáférési a biztonsági paraméterek, amelyet a biztonsági jogkivonat alapján. Miután létrehozott és állítsa be a biztonsági jogkivonatot, akkor lesz szükség az összes CDN végponti URL-címek. Azonban az URL-Újraírási szabályt, mert a SAS-jogkivonat nem szükséges a CDN-végponton. A SAS-jogkivonat később érvénytelenné válik, ha az Azure CDN már nem lesz képes kísérelje meg újra érvényesítését a tartalmat a forráskiszolgálóról.

1. [Hozzon létre egy Azure CDN biztonsági jogkivonat](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) , és aktiválja a rules engine használatával a CDN-végpont és az elérési utat, ahol a felhasználók férhetnek hozzá a fájlhoz.

   Egy biztonsági jogkivonat-végpont URL-cím formátuma a következő:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Példa:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   A paraméterbeállításokkal a biztonsági jogkivonat-hitelesítésre eltérnek a paraméterbeállításokkal egy SAS-token. Amikor létrehoz egy biztonsági jogkivonat lejárati idő használatát választja, ha az értéke megegyezik a SAS-jogkivonat lejárati idejének kell beállítania. Így biztosítható, hogy kiszámítható legyen-e a lejárati időt. 
 
2. Használja a [szabálymotorral](cdn-rules-engine.md) SAS-token hozzáférést a tárolóban lévő összes BLOB URL-újraíró szabály létrehozásához. Új szabályok propagálása akár 4 óra múlva.

   Az alábbi minta URL-újraíró szabály egy reguláris kifejezési mintája rögzítését csoport és a egy nevű végpontot használ *sasstoragedemo*:
   
   Forrás:   
   `(container1\/.*)`
   
   Cél:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![A CDN URL-újraíró szabály – bal](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL-újraíró szabály – jobbra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Ha megújítja az SAS, győződjön meg arról, hogy az új SAS-jogkivonat használatával frissítse az Url-Újraírási szabályt. 

## <a name="sas-parameter-considerations"></a>SAS paraméterrel kapcsolatos szempontok

SAS-paraméterek az Azure CDN nem láthatók el, mert az Azure CDN azokon kézbesítési működését nem módosítható. A paraméter van megadva korlátozások vonatkoznak, csak a kéréseket, amelyek az Azure CDN a forráskiszolgáló, az ügyfél kéréseit, az Azure CDN esetében nem hajt végre. Ezt a különbséget fontos figyelembe venni a SAS-paraméterek beállításakor. Ha ezeket a speciális funkciók szükségesek, és használja [beállítást 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), a megfelelő korlátozások beállítása az Azure CDN biztonsági jogkivonatot.

| SAS-paraméter neve | Leírás |
| --- | --- |
| Indítás | Az az idő, amely az Azure CDN megkezdheti a blob fájl elérésére. Óra miatt tevékenységdiagramon (érkezésekor óra jel különböző időpontokban különböző összetevők), válassza a 15 perccel korábbi, ha azt szeretné, hogy az eszköz azonnal elérhető legyen. |
| Vége | Az idő, amely után az Azure CDN már nem tud hozzáférni a blobfájlba. Előzőleg gyorsítótárazott Azure CDN-en fájlok továbbra is elérhető. Szabályozhatja a lejárati időpont, vagy a megfelelő lejárati idő beállítása az Azure CDN biztonsági jogkivonat, vagy az eszköz törlése. |
| Engedélyezett IP-címek | Választható. Ha használ **verizon Azure CDN**, és a meghatározott tartományok ezzel a paraméterrel megadható [Azure CDN a Verizon peremhálózati kiszolgáló IP-címtartományok](https://msdn.microsoft.com/library/mt757330.aspx). Ha használ **Akamai Azure CDN**, az IP-címtartományok paraméter nem állítható be, mert nem statikus IP-címeket.|
| Engedélyezett protokollok | Az a fiók SAS kódjával végzett kérelemhez engedélyezett protokoll(ok). A HTTPS beállítás ajánlott.|

## <a name="next-steps"></a>További lépések

SAS kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Közös hozzáférésű jogosultságkódok (SAS) használata](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [A közös hozzáférésű Jogosultságkódot, 2. rész: Hozzon létre, és használhatja az SAS-Blob-tárolóval](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Jogkivonat-hitelesítés beállításával kapcsolatos további információkért lásd: [biztonságossá tétele az Azure Content Delivery Network objektumok jogkivonat-hitelesítéssel](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
