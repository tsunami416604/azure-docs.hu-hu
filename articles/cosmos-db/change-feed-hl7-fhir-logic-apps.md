---
title: "Adatcsatorna HL7 FHIR erőforrások - Azure Cosmos DB módosítása |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthatja be a változási értesítéseket HL7 FHIR beteg egészségügyi rekordok Azure Logic Apps, az Azure Cosmos DB és a Service Bus használatával."
keywords: HL7 fhir
services: cosmos-db
author: hedidin
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 0d25c11f-9197-419a-aa19-4614c6ab2d06
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: b-hoedid
ms.openlocfilehash: d2b50c0b6864af41fb9cfa051721c432772b228d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>A Logic Apps és az Azure Cosmos DB használatával HL7 FHIR egészségügyi rekord módosítása betegek értesítése

Az Azure MVP Howard Edidin nemrég egészségügyi szervezetek, amelyek hozzáadni az új funkciókat a beteg portálra felvenni a kapcsolatot. Értesítések küldéséhez meghozandó, az egészségügyi frissítették, és szeretne előfizetni a frissítések betegek szükség rájuk szükség rájuk. 

Ez a cikk végigvezeti a módosítás hírcsatorna a egészségügyi szervezet Azure Cosmos DB, a Logic Apps és a Service Bus használatával létrehozott értesítési megoldást. 

## <a name="project-requirements"></a>Projekt követelmények
- Szolgáltatók küldése HL7 konszolidált klinikai dokumentum architektúra (C-CDA) dokumentumok XML formátumban. C-CDA dokumentumok tartalmazzák a klinikai dokumentum, beleértve a klinikai dokumentumok például családba tartozó alábbi előzményeinek és azon rögzíti, valamint a felügyeleti, a munkafolyamat és a pénzügyi dokumentumok szinte bármilyen típusú. 
- C-CDA dokumentumok alakulnak [HL7 FHIR erőforrások](http://hl7.org/fhir/2017Jan/resourcelist.html) JSON formátumban.
- Módosított FHIR erőforrás dokumentumokat küldött e-mailek JSON formátumban.

## <a name="solution-workflow"></a>Megoldás munkafolyamat 

Magas szinten a projekt szükséges a következő munkafolyamat-lépéseket: 
1. C-CDA dokumentumok átalakítása FHIR erőforrásokat.
2. Ciklikus lekérdezés a módosított FHIR erőforrások ismétlődő eseményindító végrehajtása. 
2. Egyéni alkalmazás, FhirNotificationApi Azure Cosmos adatbázis és az új vagy módosított dokumentumok lekérdezés való kapcsolódáshoz hívja.
3. Mentse a Service Bus várólista-válasz.
4. A lekérdezési az új üzenetek a Service Bus-üzenetsorba.
5. E-mail értesítéseket küldhet meghozandó.

## <a name="solution-architecture"></a>Megoldás architektúrája
Ez a megoldás három Logic Apps a fenti követelményeknek, és a megoldás munkafolyamat befejezéséhez szükséges. A három a logic apps a következők:
1. **HL7-FHIR-leképezés app**: a HL7 C-CDA dokumentum kap, átalakítja a FHIR erőforráshoz, majd menti azt az Azure Cosmos-Adatbázishoz.
2. **EHR app**: az Azure Cosmos DB FHIR tárház lekérdezése, és menti a válasz a Service Bus-üzenetsorba. A logikai alkalmazást használ egy [API-alkalmazás](#api-app) új és módosított dokumentumok beolvasása.
3. **Folyamat értesítési app**: a FHIR erőforrás dokumentumok e-mailben értesítést küld a törzsében.

![A három Logic Apps a HL7 FHIR egészségügyi megoldásban használt](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>A megoldásban használt Azure-szolgáltatások

#### <a name="azure-cosmos-db-documentdb-api"></a>Az Azure Cosmos DB DocumentDB API
Azure Cosmos-adatbázis a FHIR erőforrások tárháza, az alábbi ábrán látható módon.

![A HL7 FHIR egészségügyi oktatóanyagban használt Azure Cosmos DB fiók](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Logic Apps alkalmazásokat kezeléséhez a munkafolyamat. Az alábbi képek a létre ebben a megoldásban a Logic apps megjelenítése. 


1. **HL7-FHIR-leképezés app**: megkapja a HL7 C-CDA dokumentumot, és irányítópulttá, használja a vállalati integrációs csomag Logic Apps FHIR erőforráshoz. A vállalati integrációs csomag kezeli a leképezés a C-CDA FHIR erőforrásokhoz.

    ![A logikai alkalmazás HL7 FHIR egészségügyi rekordok fogadására szolgáló](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **EHR app**: az Azure Cosmos DB FHIR tárház lekérdezése, és mentse a válasz a Service Bus-üzenetsorba. A GetNewOrModifiedFHIRDocuments alkalmazás kódja nem éri el.

    ![A logikai alkalmazást használt Azure Cosmos DB lekérdezése](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Folyamat értesítési app**: a FHIR erőforrás dokumentumokkal e-mail értesítés küldése a törzsében.

    ![A logikai alkalmazást a HL7 FHIR erőforrás beteg e-maileket küldő törzsében.](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
Az alábbi ábrán láthatók a betegek várólista. A címke tulajdonság értékét használja az e-mail tárgyát.

![A Service Bus-üzenetsorba HL7 FHIR oktatóanyagban használt](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>API-alkalmazás
Az API-alkalmazások Azure Cosmos adatbázis és az új vagy módosított FHIR dokumentumok erőforrástípusok szerint lekérdezések csatlakozik. Ez az alkalmazás rendelkezik egy tartományvezérlő, **FhirNotificationApi** egy művelettel **GetNewOrModifiedFhirDocuments**, lásd: [API-alkalmazás forrását](#api-app-source).

Használjuk a [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) osztály az Azure Cosmos DB DocumentDB .NET API-t. További információkért lásd: a [módosítás hírcsatorna cikk](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>GetNewOrModifiedFhirDocuments művelet

**Bemenetek**
- DatabaseId
- CollectionId
- HL7 FHIR erőforrástípus neve
- Logikai érték: Indítsa el az elejétől
- Int: Visszaadott dokumentumok száma

**Kimenetek**
- Sikerült: Állapotkód: 200, válasz: dokumentumok (JSON-tömb) listája
- Hiba: Állapotkód: 404-es, válasz: "nem található a"*erőforrás neve "* erőforrástípus"

<a id="api-app-source"></a>

**Az API-alkalmazás forrása**

```C#

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>A FhirNotificationApi tesztelése 

A következő kép bemutatja, hogyan swagger lett megadva a tesztelése a [FhirNotificationApi](#api-app-source).

![A Swagger-fájl, amellyel tesztelheti az API-alkalmazás](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Azure-portál irányítópultjának

A következő kép bemutatja az összes Azure-szolgáltatás fut az Azure portálon ebben a megoldásban.

![Az Azure-portálon megjelenítő HL7 FHIR oktatóanyagban használt minden szolgáltatás](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Összefoglalás

- Megtanulta, hogy Azure Cosmos DB natív támogatja a következő kapcsolattípust értesítéseket az új vagy módosított dokumentumokat és milyen egyszerűen használatára van-e. 
- A Logic Apps használatával programozás nélkül munkafolyamatokat hozhat létre.
- Azure Service Bus-üzenetsorok használatával a terjesztési HL7 FHIR dokumentumok kezeléséhez.

## <a name="next-steps"></a>Következő lépések
Azure Cosmos DB kapcsolatos további információkért tekintse meg a [Azure Cosmos DB kezdőlap](https://azure.microsoft.com/services/cosmos-db/). A Logic Apps kapcsolatos további információk megadására, lásd: [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


