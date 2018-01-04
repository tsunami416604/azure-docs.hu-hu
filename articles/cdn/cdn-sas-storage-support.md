---
title: "Az Azure CDN használatával a SAS használatával |} Microsoft Docs"
description: 
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: rli v-deasim
ms.openlocfilehash: de30f4319be75362131f8c8ad71aad57b0528f05
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>Az Azure CDN használatával a SAS használatával

Szolgálnak a tárolót a tárfiókja tartalmat, amikor érdemes lehet biztonságos hogyan felhasználók férhetnek hozzá a fájlok privát hozzáférést ahhoz a tárolóhoz. Ellenkező esetben a tároló, amelynek nyilvános hozzáféréssel rendelkezik elérhető bárki, aki ismeri az URL-CÍMÉT. Korlátozott hozzáférést biztosít a tároló titkos védelméhez, amelyek engedélyezte az content delivery network (CDN) elérésére, használhatja az Azure storage közös hozzáférésű Jogosultságkód (SAS) funkciót.

SAS-kód egy URI-t, hogy biztosít anélkül, hogy a fiókkulcs hozzáférési jogosultsága ahhoz, hogy az Azure Storage-erőforrások korlátozva. SAS-kód nem bízik meg a tárfiók kulcsára, az ügyfelek, de, akinek kíván hozzáférést biztosíthat bizonyos tárfiók erőforrásainak biztosíthat. Azáltal, hogy a közös hozzáférésű jogosultságkód URI ezekre az ügyfelekre, akkor adja meg számukra erőforráshoz való hozzáférés egy megadott ideig.
 
SAS megadhatja a hozzáférés különböző paraméterek egy blobba, például a kezdő és lejárati idejét, engedélyek (olvasás/írás) és IP-címtartományok. Ez a cikk ismerteti a SAS, az Azure CDN együtt használja. További információ a SAS-figyeléséről, valamint azt, és a paraméter beállítások létrehozásához lásd: [használata közös hozzáférésű jogosultságkód (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Azure CDN beállítani, hogy a tároló SAS
A következő három módon SAS használatával az Azure CDN szolgáltatás használata ajánlott. Minden beállítás azt feltételezik, hogy már létrehozott egy működő SAS (lásd). 
 
### <a name="prerequisites"></a>Előfeltételek
Indítsa el, hozzon létre egy tárfiókot, és előállít egy SAS-kód az eszközhöz. Kétféle tárolt hozzáférési aláírásokkal hozhat létre: a szolgáltatásalapú SAS- vagy SAS fiókkal. További információkért lásd: [közös hozzáférésű jogosultságkód típusú](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Miután létrehozta a SAS-kód, a blob storage fájl formátuma a következő URL-címmel érhető el:`https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
Példa:
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Beállítás paraméterekkel kapcsolatos további információkért lásd: [SAS paraméter szempontok](#sas-parameter-considerations) és [megosztott hozzáférési aláírást paraméterek](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![CDN SAS-beállítások](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>1. lehetőség: Kideríthessék SAS áteresztő blob-tároló a CDN

Ez a beállítás a legegyszerűbb, és csak egyetlen SAS tokent használ, az eredeti kiszolgálóra a CDN átadott. Támogatják a **Azure CDN Verizon**, a Standard és prémium szintű profilok és **Akamai Azure CDN**. 
 
1. Jelölje ki azt a végpont **szabályok gyorsítótárazás**, majd jelölje be **minden egyedi URL-cím gyorsítótárazása** a a **lekérdezési karakterláncok gyorsítótárazása** lista.

    ![CDN-gyorsítótárazási szabályok](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Telepítése után SAS a tárfiókon, használja a SAS-jogkivonat a CDN URL-címet a fájl elérésére. 
   
   Az eredményül kapott URL-cím formátuma a következő:`https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   Példa:   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. A gyorsítótárazás időtartama finomhangolásához gyorsítótárazási szabályokkal vagy hozzáadásával `Cache-Control` fejlécek forráson. Mivel a CDN a SAS-jogkivonat egyszerű lekérdezési karakterláncként kezeli, ajánlott eljárásként érdemes beállítania gyorsítótárazás időtartama. vagy az azelőtti a SAS-lejárati idő lejár. Ellenkező esetben a fájlt a hosszabb ideig tart, mint a biztonsági Társítások aktív gyorsítótárazza, ha a fájl esetleg érhető el a CDN eredeti kiszolgálóra a SAS-lejárati idő eltelte után. Ha ez történik, és engedélyezni szeretné a gyorsítótárazott fájl nem érhető el, végezze el a kiürítési művelet a fájl, törölje a jelölést a gyorsítótárból. A CDN a gyorsítótárazás időtartama beállításával kapcsolatos információkért lásd: [vezérlő Azure Content Delivery Network gyorsítótárazásának a szabályok gyorsítótárazással](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>2. lehetőség: Rejtett CDN biztonsági jogkivonat átdolgozás szabály használatával
 
Ezzel a beállítással biztosíthatja a származási blob-tároló anélkül, hogy egy SAS-jogkivonat a CDN-felhasználó. Érdemes lehet használja ezt a beállítást, ha nincs szükség speciális hozzáférési korlátozásokat a fájl, de szeretné akadályozni, hogy a felhasználók közvetlenül a CDN kiszervezési alkalommal javítása érdekében a tárolási forrás eléréséhez. Ez a beállítás csak akkor **verizon Azure CDN Premium** profilok. 
 
1. Használja a [szabálymotor](cdn-rules-engine.md) URL-újraíró szabály létrehozásához. Új szabályok való terjesztése körülbelül 90 percig tarthat.

   ![CDN kezelése gomb](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN szabályok motor gomb](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Ez a minta URL-újraíró szabály van a következő mintákat:
   
   Adatforrás:   
   `/test/demo.jpg`
   
   Cél:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN-URL-újraíró szabály](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. Hozzáférés a fájlt a CDN nélkül a SAS-jogkivonat a következő formátumban:`https://<endpoint>.azureedge.net/<folder>/<file>`
 
   Példa:   
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   Vegye figyelembe, hogy bárki, függetlenül attól, hogy használják a SAS-tokent, hozzáférhet a CDN-végpontok. 

3. A gyorsítótárazás időtartama finomhangolásához gyorsítótárazási szabályokkal vagy hozzáadásával `Cache-Control` fejlécek forráson. Mivel a CDN a SAS-jogkivonat egyszerű lekérdezési karakterláncként kezeli, ajánlott eljárásként érdemes beállítania gyorsítótárazás időtartama. vagy az azelőtti a SAS-lejárati idő lejár. Ellenkező esetben a fájlt a hosszabb ideig tart, mint a biztonsági Társítások aktív gyorsítótárazza, ha a fájl esetleg érhető el a CDN eredeti kiszolgálóra a SAS-lejárati idő eltelte után. Ha ez történik, és engedélyezni szeretné a gyorsítótárazott fájl nem érhető el, végezze el a kiürítési művelet a fájl, törölje a jelölést a gyorsítótárból. A CDN a gyorsítótárazás időtartama beállításával kapcsolatos információkért lásd: [vezérlő Azure Content Delivery Network gyorsítótárazásának a szabályok gyorsítótárazással](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>3. lehetőség: CDN biztonsági tokent használó hitelesítés használata az átírást szabály

Ez a beállítás értéke a legbiztonságosabb és testre szabható. CDN biztonsági tokent használó hitelesítés használatához rendelkeznie kell egy **verizon Azure CDN Premium** profil. Ügyfél-hozzáférési a CDN biztonsági jogkivonat beállított biztonsági paraméterek alapul. Azonban ha a biztonsági Társítások érvénytelenné válik, a CDN nem fogja tudni kísérelje meg újra érvényesítését a tartalmat a forráskiszolgálóról.

1. [Hozzon létre egy CDN biztonsági tokent](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) , és aktiválja a szabálymotor segítségével a CDN-végpont és az elérési utat, ahol a felhasználók férhetnek hozzá a fájl.

   A SAS URL-cím formátuma a következő:   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   Példa:   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   A paraméter egy CDN biztonsági jogkivonat hitelesítési beállítások eltérnek a paraméter egy SAS-jogkivonat-beállításait. Amikor létrehoz egy CDN biztonsági jogkivonat lejárati idő használata mellett dönt, állítsa a SAS-jogkivonat lejárati idejének megegyező értékűnek. Ezzel biztosítja, hogy a lejárati idő előre jelezhető. 
 
2. Használja a [szabálymotor](cdn-rules-engine.md) token újraeléréséhez összes BLOB a tárolóban levő URL-újraíró szabály létrehozásához. Új szabályok való terjesztése körülbelül 90 percig tarthat.

   Ez a minta URL-újraíró szabály van a következő mintákat:
   
   Adatforrás:   
   `/test/demo.jpg`
   
   Cél:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN-URL-újraíró szabály](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. A biztonsági Társítások megújításakor frissíteni az URL-újraíró szabály, amely az új SAS-jogkivonatot használja. 

## <a name="sas-parameter-considerations"></a>SAS paraméter kapcsolatos szempontok

SAS-paraméterek és a CDN nem láthatók el, mert a CDN a kézbesítési viselkedését azokat nem módosíthatja. A megadott paraméter csak a kérelmeket, amelyek a CDN-t az eredeti kiszolgálóra, az ügyfél kéréseit, és a CDN a nem érvényesek. Ezt a különbséget fontos figyelembe venni, ha úgy állítja be a SAS-paraméterek. Ha ezeket a lehetőségeket speciális szükségesek, és használja [beállítás 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), a megfelelő korlátozások beállítása a CDN biztonsági jogkivonatot.

| SAS-paraméter neve | Leírás |
| --- | --- |
| Indítás | Az az idő, amely a CDN akkor kezdhető meg, a blob-fájl elérésére. Óra miatt döntés (érkezésekor óra jel különböző időpontokban különböző összetevők), válassza ki a időpontot korábbi 15 perc, ha azt szeretné, azonnal elérhetővé válik, hogy az eszköz. |
| Befejezés | Az az idő, amely után a CDN már nem tud hozzáférni a blob-fájlt. Korábban a gyorsítótárazott a CDN-t a fájlok továbbra is elérhetők maradnak. A fájl lejárati idejének szabályozása, a megfelelő lejárati idő beállítása a CDN biztonsági jogkivonat, vagy az eszköz törlése. |
| Engedélyezett IP-címek | Választható. Ha használ **Azure CDN Verizon**, ennek a paraméternek is beállíthatja a meghatározott tartományok [peremhálózati kiszolgáló IP-címtartományok Verizon Azure CDN](https://msdn.microsoft.com/library/mt757330.aspx). Ha használ **Akamai Azure CDN**, az IP-címtartományok paraméter nem állítható be, mert az IP-címei nem statikus.|
| Megengedett protokollok | A SAS-fiókkal kérelme engedélyezett protokollokkal. A HTTPS beállítás ajánlott.|

## <a name="see-also"></a>Lásd még
- [Közös hozzáférésű jogosultságkód (SAS) használatával](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Közös hozzáférésű Jogosultságkód, 2. rész: Létrehozása és SAS-kód használata a Blob-tároló](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [Tokent használó hitelesítés az Azure Content Delivery Network eszközök védelme](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
