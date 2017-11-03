---
title: "Hogyan Blitline használandó kép feldolgozása - Azure szolgáltatás útmutató"
description: "Megtudhatja, hogyan használhatja az Azure alkalmazáshoz tartozó képek feldolgozásához Blitline szolgáltatást."
services: 
documentationcenter: .net
author: blitline-dev
manager: jason@blitline.com
editor: jason@blitline.com
ms.assetid: 6c711248-0e52-4895-ba9e-8395628de924
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2014
ms.author: support@blitline.com
ms.openlocfilehash: 1d90599e028b3407a513b04b878e3aefc39928a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Az Azure és az Azure Storage Blitline használata
Ez az útmutató alapján meghatározható, Blitline szolgáltatások eléréséhez és az Blitline feladatok elküldéséhez.

## <a name="what-is-blitline"></a>Mi az a Blitline?
Blitline a felhő alapú lemezkép feldolgozási szolgáltatás, amely biztosít a vállalati szintű kép feldolgozását építheti fel saját maga szeretné költség ár töredéke alatt.

Az a tény, hogy lemezkép nincs feldolgozva többször, általában az újonnan létrehozott minden webhely az alapoktól. A Microsoft vegye figyelembe, ez mivel azt létrehozott őket egy millió alkalommal túl. Egy nap döntöttünk, hogy lehet, hogy a rendszer idő azt csak erre mindenki számára. Tudjuk, hogyan kell tenni, akkor gyors és hatékony, és időközben mentése mindenki munkahelyi.

További információkért lásd: [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>Mi Blitline nincs...
Elmagyarázza, mit Blitline hasznos, ha, nem gyakran könnyebben azonosíthatja a Blitline nem mire soron előtt.

* Blitline nincs HTML widgeteket, hogy a képek feltöltése. Lemezképeket nyilvánosan vagy az elérni kívánt Blitline érhető el a korlátozott engedélyekkel kell rendelkeznie.
* Blitline nem végez feldolgozást, például a Aviary.com élő kép
* Blitline nem fogadja el a képek feltöltését, nem lehet leküldéssel terjeszteni a képek a Blitline közvetlenül. Kell küldje le őket az Azure Storage vagy más helyen Blitline támogatja, és azokat hol Blitline beállítják.
* Blitline nagymértékben párhuzamos, és nem végez semmilyen szinkron feldolgozásra. Ezért meg kell biztosítják a postback_url, és azt segítségével megállapíthatja, hogy ha igazolnia befejezése feldolgozásra.

## <a name="create-a-blitline-account"></a>Blitline-fiók létrehozása
[!INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Egy Blitline feladat létrehozása
Blitline JSON használja a műveletek végrehajtása a lemezkép kívánt adhatók meg. Ez a JSON néhány egyszerű mezők tevődik össze.

A legegyszerűbb például a következőképpen történik:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Itt a "src" lemezkép kezeléséért JSON tudunk "... boys.jpeg", majd a majd módosítsa az adott 240 x 140 lemezképet.

Alkalmazásazonosító valami megtalálja a **KAPCSOLATINFORMÁCIÓ** vagy **kezelése** Azure fülre. A titkos azonosítója, amely lehetővé teszi a Blitline feladatok futtatása is.

A "Mentés" paraméter azonosítja, ahol el szeretné helyezni a kép, ha azt feldolgozta kapcsolatos információkat. A trivial esetben még nem meghatározott egyet. Ha nincs megadva helykód Blitline fogja tárolni, helyileg (és ideiglenesen) egy egyedi felhő helyen. Erre a helyre beszerezni a JSON Blitline által visszaadott, amikor a Blitline lehet. A "lemezképpel" azonosító szükséges, ezért Önnek mentésekor azonosításához az adott képet ad vissza.

További információt talál arról a *funkciók* támogatjuk itt: <http://www.blitline.com/docs/functions>

A feladat beállítások itt dokumentációjában talál: <http://www.blitline.com/docs/api>

Ha elvégezte a JSON csak annyit kell tennie az **POST** úgy, hogy`http://api.blitline.com/job`

Az alábbihoz hasonló JSON vissza jelenik meg:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Igen, akkor Blitline megkapta a kérelmet, azt állította a feldolgozási sorban, és befejezését követően a kép lesz elérhető legyen a következőn: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_azonosítója / CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>A képfájl mentése az Azure Storage-fiókhoz
Ha egy Azure Storage-fiókot, könnyedén lehet Blitline a feldolgozott képek leküldéses az Azure-tárolóba. Egy "azure_destination" hozzáadásával határozza meg a hely és a Blitline, amelyekkel a.

Például:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


A saját CAPITALIZED értékek kitöltésével elküldheti a http://api.blitline.com/job a JSON és a "src" kép rendszer dolgozza fel a szűrőéhez, majd majd leküldött Azure célhelyre.

### <a name="please-note"></a>Megjegyzés:
A biztonsági Társítások teljes SAS URL-címét, beleértve a fájlnevet a célfájl kell tartalmaznia.

Példa:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


A legújabb kiadása Blitline tartozó Azure Storage docs is olvasható [Itt](http://www.blitline.com/docs/azure_storage).

## <a name="next-steps"></a>Következő lépések
Látogasson el az egyéb funkciókkal kapcsolatos olvasni blitline.com:

* Blitline API-végpont Docs <http://www.blitline.com/docs/api>
* Blitline API-függvények <http://www.blitline.com/docs/functions>
* Blitline API példák <http://www.blitline.com/docs/examples>
* Harmadik része a Nuget könyvtár <http://nuget.org/packages/Blitline.Net>

