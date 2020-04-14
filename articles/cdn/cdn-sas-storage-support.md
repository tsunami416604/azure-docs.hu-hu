---
title: Az Azure CDN használata a SAS-szal | Microsoft dokumentumok
description: Az Azure CDN támogatja a megosztott hozzáférésű aláírás (SAS) használatát a magántárolók korlátozott hozzáférésének biztosításához.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: c2580aa4ee22996c1bf0fe5c86064a6543450071
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260173"
---
# <a name="using-azure-cdn-with-sas"></a>Az Azure CDN használata a SAS-szal

Amikor beállít egy tárfiókot az Azure Content Delivery Network (CDN) számára a tartalom gyorsítótárazására, alapértelmezés szerint bárki, aki ismeri a tárolók URL-címeit, hozzáférhet a feltöltött fájlokhoz. A tárfiókban lévő fájlok védelme érdekében beállíthatja a tárolótárolók nyilvánosról privátra való hozzáférését. Ha azonban így tesz, senki sem férhet hozzá a fájlokhoz. 

Ha korlátozott hozzáférést szeretne adni privát tárolókhoz, használhatja Azure-tárfiókjának közös hozzáférésű jogosultságkód (SAS) funkcióját. A SAS olyan URI, amely az Azure Storage erőforrásainak korlátozott hozzáférési jogosultságát biztosítja anélkül, hogy közzétenné a fiók kulcsát. SAS-t adhat olyan ügyfeleknek, amelyekben nem bízik meg a tárfiók kulcsával, de akiknek hozzáférést szeretne delegálni bizonyos tárfiók-erőforrásokhoz. Ha megosztott hozzáférésű aláírásuri-t oszt ki ezeknek az ügyfeleknek, egy adott ideig hozzáférést biztosít számukra egy erőforráshoz.
 
A SAS segítségével különböző paramétereket definiálhat egy blobhoz való hozzáféréshez, például a kezdési és lejárati időket, az engedélyeket (olvasási/írási) és az IP-tartományokat. Ez a cikk ismerteti, hogyan kell használni a SAS együtt Azure CDN. A SAS-ről, többek között a létrehozásáról és a paraméterbeállításairól a [Megosztott hozzáférésű aláírások (SAS) használata című](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)témakörben talál további információt.

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Az Azure CDN beállítása a storage SAS-szal való együttműködésre
A SAS Azure CDN-nel való használatához a következő három beállítás ajánlott. Minden beállítás feltételezi, hogy már létrehozott egy működő SAS-t (lásd az előfeltételeket). 
 
### <a name="prerequisites"></a>Előfeltételek
A kezdéshez hozzon létre egy tárfiókot, majd hozzon létre egy SAS-t az eszközhöz. Kétféle tárolt hozzáférési aláírást hozhat létre: egy szolgáltatás SAS-t vagy egy fiók SAS-t. További információ: [Types of shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Miután létrehozott egy SAS-jogkivonatot, az URL-cím `?sv=<SAS token>` hozzáfűzésével elérheti a blob storage-fájlt. Ennek az URL-címnek a következő formátuma van: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Például:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

A paraméterek beállításáról a [SAS paraméterekkel kapcsolatos szempontok](#sas-parameter-considerations) és [a megosztott hozzáférésű aláírás paraméterei](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works)című témakörben talál további információt.

![CDN SAS-beállítások](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>1. lehetőség: A SAS használata átmenő legátakablob-tárolóba az Azure CDN-ből

Ez a beállítás a legegyszerűbb, és egyetlen SAS-jogkivonatot használ, amely az Azure CDN-ből az eredeti kiszolgálóra kerül.
 
1. Jelöljön ki egy végpontot, válassza **a Gyorsítótárazási szabályok lehetőséget,** majd válassza a **Lekérdezési karakterlánc gyorsítótárazási** listájának **minden egyedi URL-címének gyorsítótárazását.**

    ![CDN-gyorsítótárazási szabályok](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Miután beállította a SAS-t a tárfiókban, a SAS-jogkivonatot a CDN-végpontdal és az eredeti kiszolgáló URL-címekkel együtt kell használnia a fájl eléréséhez. 
   
   Az eredményül kapott CDN-végpont URL-címe a következő formátumú:`https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Például:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Finomítsa a gyorsítótár időtartamát gyorsítótárazási `Cache-Control` szabályok használatával vagy fejlécek hozzáadásával az eredeti kiszolgálón. Mivel az Azure CDN a SAS-jogkivonatot egyszerű lekérdezési karakterláncként kezeli, ajánlott eljárásként be kell állítania egy gyorsítótárazási időtartamot, amely a SAS lejárati ideje alatt vagy azt megelőzően lejár. Ellenkező esetben, ha egy fájl gyorsítótárazott hosszabb ideig, mint a SAS aktív, a fájl elérhető lehet az Azure CDN forráskiszolgálóután a SAS lejárati idő telt el. Ha ez a helyzet bekövetkezik, és a gyorsítótárazott fájlt elérhetetlenné szeretné tenni, a gyorsítótárból való törléshez el kell végeznie a fájl kiürítési műveletét. A gyorsítótár időtartamának az Azure CDN-en való beállításáról az [Azure CDN-gyorsítótárazási viselkedésének szabályozása gyorsítótárazási szabályokkal](cdn-caching-rules.md)című témakörben talál.

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>2. lehetőség: Rejtett CDN SAS-token átírási szabállyal
 
Ez a beállítás csak a **Verizon-profilokból származó Azure CDN Premium esetén** érhető el. Ezzel a beállítással biztonságossá teheti a blob tárolóját az eredeti kiszolgálón. Érdemes lehet használni ezt a lehetőséget, ha nincs szüksége speciális hozzáférési korlátozások a fájlhoz, de szeretné megakadályozni, hogy a felhasználók közvetlenül hozzáférjenek a tároló eredetéhez az Azure CDN kiszervezési idejének javítása érdekében. A SAS-jogkivonat, amely ismeretlen a felhasználó számára, szükséges bárki számára, aki hozzáfér a fájlokhoz az eredeti kiszolgáló megadott tárolójában. Azonban az URL-újraírási szabály miatt a SAS-jogkivonat nem szükséges a CDN-végponton.
 
1. A [szabálymotor segítségével](cdn-rules-engine.md) hozzon létre egy URL-újraírási szabályt. Az új szabályok terjesztése akár 4 órát is igénybe vehet.

   ![CDN-kezelés gomb](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN szabályok motorgomb](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   A következő minta URL-újraírási szabály reguláris kifejezésmintát használ egy rögzítési csoporttal és egy *sasstoragedemo*nevű végpontot:
   
   Forrás:   
   `(container1\/.*)`
   
   Cél:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL újraírási](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![szabály - bal CDN URL újraírási szabály - jobbra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Miután az új szabály aktívvá válik, bárki hozzáférhet a CDN-végpont megadott tárolójában lévő fájlokhoz, függetlenül attól, hogy az URL-címben SAS-jogkivonatot használnak-e. Itt van a formátum:`https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Például:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Finomítsa a gyorsítótár időtartamát gyorsítótárazási `Cache-Control` szabályok használatával vagy fejlécek hozzáadásával az eredeti kiszolgálón. Mivel az Azure CDN a SAS-jogkivonatot egyszerű lekérdezési karakterláncként kezeli, ajánlott eljárásként be kell állítania egy gyorsítótárazási időtartamot, amely a SAS lejárati ideje alatt vagy azt megelőzően lejár. Ellenkező esetben, ha egy fájl gyorsítótárazott hosszabb ideig, mint a SAS aktív, a fájl elérhető lehet az Azure CDN forráskiszolgálóután a SAS lejárati idő telt el. Ha ez a helyzet bekövetkezik, és a gyorsítótárazott fájlt elérhetetlenné szeretné tenni, a gyorsítótárból való törléshez el kell végeznie a fájl kiürítési műveletét. A gyorsítótár időtartamának az Azure CDN-en való beállításáról az [Azure CDN-gyorsítótárazási viselkedésének szabályozása gyorsítótárazási szabályokkal](cdn-caching-rules.md)című témakörben talál.

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>3. lehetőség: CDN biztonsági tokenhitelesítés használata újraírási szabállyal

Az Azure CDN biztonsági token hitelesítésének használatához a **Verizon-profilból származó Azure CDN Premium szolgáltatással kell rendelkeznie.** Ez a beállítás a legbiztonságosabb és testreszabható. Az ügyfélhozzáférés a biztonsági jogkivonaton beállított biztonsági paramétereken alapul. Miután létrehozta és beállította a biztonsági jogkivonatot, minden CDN-végpont URL-címén szükség lesz rá. Azonban az URL-újraírási szabály miatt a SAS-jogkivonat nem szükséges a CDN-végponton. Ha a SAS-jogkivonat később érvénytelenné válik, az Azure CDN már nem lesz képes újraérvényesíteni a tartalmat az eredeti kiszolgálóról.

1. [Hozzon létre egy Azure CDN biztonsági jogkivonatot,](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) és aktiválja azt a CDN-végpont szabálymotorjával és elérési úttal, ahol a felhasználók hozzáférhetnek a fájlhoz.

   A biztonsági jogkivonat végpontjának URL-címe a következő formátumú:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Például:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   A biztonsági jogkivonat-hitelesítés paraméterbeállításai eltérnek a SAS-jogkivonat paraméterbeállításaitól. Ha úgy dönt, hogy egy biztonsági jogkivonat létrehozásakor lejárati időt használ, állítsa be a SAS-jogkivonat lejárati idejét. Ezzel biztosítja, hogy a lejárati idő kiszámítható legyen. 
 
2. A [szabálymotor](cdn-rules-engine.md) használatával hozzon létre egy URL-újraírási szabályt, amely lehetővé teszi a SAS-jogkivonat-hozzáférést a tárolóban lévő összes blobhoz. Az új szabályok terjesztése akár 4 órát is igénybe vehet.

   A következő minta URL-újraírási szabály reguláris kifejezésmintát használ egy rögzítési csoporttal és egy *sasstoragedemo*nevű végpontot:
   
   Forrás:   
   `(container1\/.*)`
   
   Cél:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL újraírási](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![szabály - bal CDN URL újraírási szabály - jobbra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Ha megújítja a SAS, győződjön meg arról, hogy frissíti az URL-újraírási szabályt az új SAS-jogkivonattal. 

## <a name="sas-parameter-considerations"></a>SAS-paraméterekkel kapcsolatos szempontok

Mivel a SAS-paraméterek nem láthatók az Azure CDN számára, az Azure CDN nem módosíthatja a kézbesítési viselkedését azok alapján. A meghatározott paraméterkorlátozások csak az okra a kérelmekre vonatkoznak, amelyeket az Azure CDN az eredeti kiszolgálóra küld, az ügyféltől az Azure CDN-hez érkező kérelmekre nem. Ezt a megkülönböztetést fontos figyelembe venni a SAS-paraméterek beállításakor. Ha ezek a speciális képességek szükségesek, és a [3.](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)

| SAS-paraméter neve | Leírás |
| --- | --- |
| Indítás | Az az időpont, amikor az Azure CDN megkezdheti a blobfájl elérését. Az óra döntésének köszönhetően (amikor az órajel különböző időpontokban érkezik a különböző összetevőkhöz), válasszon egy időpontot 15 perccel korábban, ha azt szeretné, hogy az eszköz azonnal elérhető legyen. |
| Befejezés | Az az idő, amely után az Azure CDN már nem tudja elérni a blob fájlt. Az Azure CDN-en korábban gyorsítótárazott fájlok továbbra is elérhetők. A fájl lejárati idejének szabályozásához állítsa be a megfelelő lejárati időt az Azure CDN biztonsági jogkivonaton, vagy ürítse ki az eszközt. |
| Engedélyezett IP-címek | Választható. Ha a **Verizon Azure CDN-t**használja, beállíthatja ezt a paramétert az Azure CDN-ben a [Verizon Edge Server IP-tartományaiból meghatározott tartományokra.](/azure/cdn/cdn-pop-list-api) Ha az **Akamai Azure CDN-t**használja, nem állíthatja be az IP-tartományok paramétert, mert az IP-címek nem statikusak.|
| Engedélyezett protokollok | A protokoll(ok) lehetővé tették a SAS fiókkal benyújtott kérelmet. A HTTPS beállítás ajánlott.|

## <a name="next-steps"></a>További lépések

A SAS-ről további információt az alábbi cikkekben talál:
- [Közös hozzáférésű jogosultságkódok (SAS) használata](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Megosztott hozzáférésű aláírások, 2. rész: SAS létrehozása és használata blobtármal](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

A tokenhitelesítés beállításáról az [Azure Content Delivery Network-eszközök védelme tokenhitelesítéssel](https://docs.microsoft.com/azure/cdn/cdn-token-auth)című témakörben talál további információt.
