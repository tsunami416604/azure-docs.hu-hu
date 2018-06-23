---
title: Az Azure CDN használatával a SAS használatával |} Microsoft Docs
description: Az Azure CDN a közös hozzáférésű Jogosultságkód (SAS) korlátozott hozzáférést biztosít a tároló titkos használatát támogatja.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: v-deasim
ms.openlocfilehash: 15a4e0a8d62b38fa7aa542d95e53d29621965666
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316568"
---
# <a name="using-azure-cdn-with-sas"></a>Az Azure CDN használatával a SAS használatával

Miután beállította a storage-fiók az Azure tartalom Delivery Network (CDN) alapértelmezés szerint a gyorsítótár teljes tartalmát, segítségével bárki, aki ismeri a tárolókban URL-férhetnek hozzá az Ön által feltöltött fájlok. A tárfiókban lévő fájlok védelme érdekében beállíthatja a tárolókban magán a nyilvános elérhetőségét. Azonban ha így tesz, nem fogja tudni érni a fájlokat. 

Ha azt szeretné, korlátozott hozzáférést biztosít a tároló privát, használhatja a közös hozzáférésű Jogosultságkód (SAS) szolgáltatást, az Azure-tárfiókot. SAS-kód egy URI-t, hogy biztosít anélkül, hogy a fiókkulcs hozzáférési jogosultsága ahhoz, hogy az Azure Storage-erőforrások korlátozva. SAS-kód nem bízik meg a tárfiók kulcsára, az ügyfelek, de, akinek kíván hozzáférést biztosíthat bizonyos tárfiók erőforrásainak biztosíthat. Azáltal, hogy a közös hozzáférésű jogosultságkód URI ezekre az ügyfelekre, akkor adja meg számukra erőforráshoz való hozzáférés egy megadott ideig.
 
SAS-kód, a szolgáltatás segítségével megadhatja a hozzáférés különböző paraméterek egy blobba, például a kezdő és lejárati idejét, engedélyek (olvasás/írás) és IP-címtartományok. Ez a cikk ismerteti a SAS, az Azure CDN együtt használja. További információ a SAS-figyeléséről, valamint azt, és a paraméter beállítások létrehozásához lásd: [használata közös hozzáférésű jogosultságkód (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Azure CDN beállítani, hogy a tároló SAS
A következő három módon SAS használatával az Azure CDN szolgáltatás használata ajánlott. Minden beállítás azt feltételezik, hogy már létrehozott egy működő SAS (lásd). 
 
### <a name="prerequisites"></a>Előfeltételek
Indítsa el, hozzon létre egy tárfiókot, és előállít egy SAS-kód az eszközhöz. Kétféle tárolt hozzáférési aláírásokkal hozhat létre: a szolgáltatásalapú SAS- vagy SAS fiókkal. További információkért lásd: [közös hozzáférésű jogosultságkód típusú](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

A SAS-jogkivonat létrehozását követően érhető el a blob storage fájl hozzáfűzése `?sv=<SAS token>` az URL-címre. Az URL-cím formátuma a következő: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Példa:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Beállítás paraméterekkel kapcsolatos további információkért lásd: [SAS paraméter szempontok](#sas-parameter-considerations) és [megosztott hozzáférési aláírást paraméterek](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![CDN SAS-beállítások](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>1. lehetőség: SAS használatával csatlakoztatott a blob Storage Azure CDN

Ez a beállítás a legegyszerűbb, és egyetlen SAS tokent, az eredeti kiszolgálóra átadott Azure CDN használ. Támogatják a **Azure CDN Standard verizon** és **Azure CDN Standard Akamai** profilok. 
 
1. Válasszon ki egy végpontot, jelölje be **szabályok gyorsítótárazás**, majd jelölje be **minden egyedi URL-cím gyorsítótárazása** a a **lekérdezési karakterláncok gyorsítótárazása** listája.

    ![CDN-gyorsítótárazási szabályok](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Telepítése után SAS a tárfiókon, a fájl eléréséhez a CDN végpont és a forrás URL-eket kell használnia a SAS-jogkivonat. 
   
   Az eredményül kapott CDN végponti URL-cím formátuma a következő: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Példa:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. A gyorsítótárazás időtartama finomhangolásához gyorsítótárazási szabályokkal vagy hozzáadásával `Cache-Control` fejlécek az eredeti kiszolgálón. Azure CDN kezeli a SAS-jogkivonat egyszerű lekérdezési karakterláncként, mert ajánlott eljárásként érdemes beállítania gyorsítótárazás időtartama. vagy az azelőtti a SAS-lejárati idő lejár. Ellenkező esetben a fájlt a hosszabb ideig tart, mint a biztonsági Társítások aktív gyorsítótárazza, ha a fájl esetleg érhető el az Azure CDN eredeti kiszolgálóra a SAS-lejárati idő eltelte után. Ha ez a helyzet akkor fordul elő, és engedélyezni szeretné a gyorsítótárazott fájl nem érhető el, végezze el a kiürítési művelet a fájl, törölje a jelölést a gyorsítótárból. A gyorsítótárazás időtartama az Azure CDN beállításával kapcsolatos információkért lásd: [vezérlő Azure CDN szolgáltatás használata a szabályok gyorsítótárazással gyorsítótárazásának](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>2. lehetőség: Rejtett átdolgozás szabállyal CDN SAS-jogkivonat
 
Ez a beállítás csak akkor **verizon Azure CDN Premium** profilok. Ezzel a beállítással biztosíthatja a blob-tárolóból a(z) az eredeti kiszolgálóra. Érdemes lehet használja ezt a beállítást, ha nincs szükség speciális hozzáférési korlátozásokat a fájl, de szeretné akadályozni, hogy a felhasználók közvetlenül az Azure CDN kiszervezési alkalommal javítása érdekében a tárolási forrás eléréséhez. A SAS-jogkivonat, amely a felhasználó számára ismeretlen, bárki, aki a fájlokat az eredeti kiszolgálóra tárolócsoportbeli blobtároló eléréséhez szükség. Azonban az URL-újraíró szabály miatt a SAS-jogkivonat nem szükséges a CDN-végpontot.
 
1. Használja a [szabálymotor](cdn-rules-engine.md) URL-újraíró szabály létrehozásához. Új szabályok körülbelül 10 percig propagálása igénybe vehet.

   ![CDN kezelése gomb](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN szabályok motor gomb](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Az alábbi minta URL-újraíró szabály egy reguláris kifejezési minta használ rögzítésével csoport és egy végpontot, nevű *storagedemo*:
   
   Forrás:   
   `(\/container1\/.*)`
   
   Cél:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN-URL-újraíró szabály - balra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL-újraíró szabály – jobb oldali](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-2.png)

2. Után az új szabály válik aktívvá, bárki hozzáférhet a megadott tároló a CDN-végpont, függetlenül attól, hogy használatát egy SAS-jogkivonatot az URL-címben lévő fájlok. A formátum a következő: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Példa:   
   `https://demoendpoint.azureedge.net/container1/demo.jpg`
       

3. A gyorsítótárazás időtartama finomhangolásához gyorsítótárazási szabályokkal vagy hozzáadásával `Cache-Control` fejlécek az eredeti kiszolgálón. Azure CDN kezeli a SAS-jogkivonat egyszerű lekérdezési karakterláncként, mert ajánlott eljárásként érdemes beállítania gyorsítótárazás időtartama. vagy az azelőtti a SAS-lejárati idő lejár. Ellenkező esetben a fájlt a hosszabb ideig tart, mint a biztonsági Társítások aktív gyorsítótárazza, ha a fájl esetleg érhető el az Azure CDN eredeti kiszolgálóra a SAS-lejárati idő eltelte után. Ha ez a helyzet akkor fordul elő, és engedélyezni szeretné a gyorsítótárazott fájl nem érhető el, végezze el a kiürítési művelet a fájl, törölje a jelölést a gyorsítótárból. A gyorsítótárazás időtartama az Azure CDN beállításával kapcsolatos információkért lásd: [vezérlő Azure CDN szolgáltatás használata a szabályok gyorsítótárazással gyorsítótárazásának](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>3. lehetőség: CDN biztonsági tokent használó hitelesítés használata az átírást szabály

Azure CDN biztonsági tokent használó hitelesítés használatához rendelkeznie kell egy **verizon Azure CDN Premium** profil. Ez a beállítás értéke a legbiztonságosabb és testre szabható. Ügyfél-hozzáférési biztonsági paramétereket, be kell állítani a biztonsági jogkivonat alapul. Miután létrehozott, és állítsa be a biztonsági jogkivonat, akkor igényel az összes CDN végpont URL-cím. Azonban az URL-újraíró szabály miatt a SAS-jogkivonat nem szükséges a CDN-végpontot. A SAS-jogkivonat később érvénytelenné válik, ha Azure CDN már nem lesz képes kísérelje meg újra érvényesítését a tartalmat a forráskiszolgálóról.

1. [Hozzon létre egy Azure CDN biztonsági jogkivonatot](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) , és aktiválja a szabálymotor segítségével a CDN-végpont és az elérési utat, ahol a felhasználók férhetnek hozzá a fájl.

   A biztonsági token-végpont URL-cím formátuma a következő:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Példa:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Egy biztonsági tokent használó hitelesítés paraméter beállításai eltérnek a paraméter egy SAS-jogkivonat-beállításait. Ha szeretne használni, amikor létrehoz egy biztonsági jogkivonatot lejárati időt, akkor kell beállítania a SAS-jogkivonat lejárati idejének értékét. Ezzel biztosítja, hogy a lejárati idő előre jelezhető. 
 
2. Használja a [szabálymotor](cdn-rules-engine.md) újraeléréséhez SAS-token összes BLOB a tárolóban levő URL-újraíró szabály létrehozásához. Új szabályok körülbelül 10 percig propagálása igénybe vehet.

   Az alábbi minta URL-újraíró szabály egy reguláris kifejezési minta használ rögzítésével csoport és egy végpontot, nevű *storagedemo*:
   
   Forrás:   
   `(\/container1\/.*)`
   
   Cél:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN-URL-újraíró szabály - balra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL-újraíró szabály – jobb oldali](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-3.png)

3. A biztonsági Társítások újítsa meg, ha győződjön meg arról, hogy az URL-újraíró szabály frissíti az új SAS-jogkivonat. 

## <a name="sas-parameter-considerations"></a>SAS paraméter kapcsolatos szempontok

SAS-paraméterek és Azure CDN nem láthatók el, mert a Azure CDN nem módosítható a kézbesítési viselkedését őket. A megadott paraméter csak a kérelmek Azure CDN teszi lehetővé az eredeti kiszolgálóra Azure CDN szolgáltatás használata az ügyfél irányuló kérelmek esetén nem érvényesek. Ezt a különbséget fontos figyelembe venni, ha úgy állítja be a SAS-paraméterek. Ha ezeket a lehetőségeket speciális szükségesek, és használja [beállítás 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), a megfelelő korlátozások beállítása az Azure CDN biztonsági jogkivonatot.

| SAS-paraméter neve | Leírás |
| --- | --- |
| Indítás | Az az idő, amely az Azure CDN szolgáltatás használata akkor kezdhető meg, a blob-fájl elérésére. Óra miatt döntés (érkezésekor egy órajel különböző időpontokban különböző összetevők), válassza ki a időpontot korábbi 15 perc, ha azt szeretné, azonnal elérhetővé válik, hogy az eszköz. |
| Befejezés | Az az idő elteltével Azure CDN szolgáltatás használata már nem tud hozzáférni a blob-fájlt. Korábban a gyorsítótárazott Azure CDN szolgáltatás használata a fájlok továbbra is elérhetők maradnak. A fájl lejárati idejének szabályozása, a megfelelő lejárati idő beállítása az Azure CDN biztonsági jogkivonat, vagy az eszköz törlése. |
| Engedélyezett IP-címek | Választható. Ha használ **Azure CDN Verizon**, ennek a paraméternek is beállíthatja a meghatározott tartományok [peremhálózati kiszolgáló IP-címtartományok Verizon Azure CDN](https://msdn.microsoft.com/library/mt757330.aspx). Ha használ **Akamai Azure CDN**, az IP-címtartományok paraméter nem állítható be, mert az IP-címei nem statikus.|
| Megengedett protokollok | A SAS-fiókkal kérelme engedélyezett protokollokkal. A HTTPS beállítás ajánlott.|

## <a name="next-steps"></a>További lépések

További információ a SAS tekintse meg a következő cikkeket:
- [Közös hozzáférésű jogosultságkód (SAS) használatával](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Közös hozzáférésű Jogosultságkód, 2. rész: Létrehozása és SAS-kód használata a Blob-tároló](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Jogkivonat hitelesítési beállításával kapcsolatos további információkért lásd: [védelmét biztosító Azure Content Delivery Network eszközök token hitelesítéssel](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
