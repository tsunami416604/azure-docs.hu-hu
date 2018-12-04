---
title: A változáscsatorna HL7 FHIR-erőforrások – Azure Cosmos DB-hez
description: Ismerje meg, hogyan állítható be a módosítási értesítésekre; HL7 FHIR egészségügyi betegnyilvántartást Azure Logic Apps, az Azure Cosmos DB és a Service Bus használatával.
keywords: HL7 fhir
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: sngun
ms.openlocfilehash: 591b79d4d9d44c55c2438d872b61a90abbe6ecad
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841115"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>A Logic Apps és az Azure Cosmos DB használatával HL7 FHIR egészségügyi rekord módosításait a betegek értesítése

Az Azure MVP Howard Edidin nemrég szeretne hozzáadni az új funkció a betegek portálra egészségügyi intézmény felvenni a kapcsolatot. Értesítések küldésére a betegek, az egészségügyi rekordot, és feliratkozhat a frissítésekhez a betegek szükség rájuk szükség rájuk. 

Ez a cikk végigvezeti a változáscsatorna értesítés megoldás létrehozása az Azure Cosmos DB, a Logic Apps és a Service Bus használatával egészségügyi szervezet számára. 

## <a name="project-requirements"></a>Projekt követelményeinek
- Szolgáltatók küldése XML formátumú dokumentumok HL7 konszolidált klinikai dokumentum architektúra (C-CDA). C-CDA dokumentumok tartalmazzák a klinikai dokumentum, beleértve a klinikai dokumentumok, például családi előzményeket és azon rekordokat, valamint felügyeleti, a munkafolyamat és a pénzügyi dokumentumok szinte bármilyen típusú. 
- C-CDA dokumentumok alakulnak [HL7 FHIR erőforrások](http://hl7.org/fhir/2017Jan/resourcelist.html) JSON formátumban.
- Módosított FHIR erőforrás dokumentum elküldése e-mailes JSON formátumban.

## <a name="solution-workflow"></a>Megoldás munkafolyamata 

Magas szinten a projekthez szükséges a következő munkafolyamat-lépéseit: 
1. C-CDA-dokumentumok átalakítása FHIR-erőforrásokat.
2. Végezze el az ismétlődő eseményindító módosított FHIR-erőforrások lekérdezése. 
2. Meghív egy egyéni alkalmazást, FhirNotificationApi kapcsolódjanak az Azure Cosmos DB és az új vagy módosított dokumentumok lekérdezése.
3. A Service Bus-üzenetsorba küldött válasz mentése.
4. Lekérdezés az új üzenetek a Service Bus-üzenetsorba.
5. A betegek e-mail értesítések küldéséhez.

## <a name="solution-architecture"></a>Megoldás architektúrája
Ez a megoldás három logikai alkalmazás a fenti követelményeknek megfelelő, és a megoldás a munkafolyamat befejezéséhez szükséges. A három logikai alkalmazások a következők:
1. **HL7 FHIR-hozzárendelés alkalmazás**: a HL7 C-CDA dokumentum fogadása, alakítja át, a FHIR-erőforráshoz, majd menti azt az Azure Cosmos DB.
2. **EHR alkalmazás**: lekérdezése az Azure Cosmos DB FHIR adattárban, és menti a Service Bus-üzenetsorba küldött válasz. Ez a logikai alkalmazás használja egy [API-alkalmazás](#api-app) új és módosított dokumentumok lekérdezésének.
3. **Folyamat értesítés alkalmazás**: küld egy e-mail-értesítés a FHIR erőforrás dokumentumokat törzsében.

![A három Logic Apps, a HL7 FHIR egészségügyi megoldást](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>A megoldásban használt Azure-szolgáltatások

#### <a name="azure-cosmos-db-sql-api"></a>Az Azure Cosmos DB SQL API-hoz
Az Azure Cosmos DB a tárház FHIR erőforrások, az alábbi ábrán látható módon.

![Az egészségügyi HL7 FHIR oktatóanyagban használt Azure Cosmos DB-fiók](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
A Logic Apps kezelni a munkafolyamat folyamatában. Az alábbi képernyőfelvételnek megfelelően a Logic apps, a megoldás számára létrehozott megjelenítése. 


1. **HL7 FHIR-hozzárendelés alkalmazás**: a HL7 C-CDA dokumentum fogadása és átalakítja a Logic Apps Enterprise Integration Pack használatával FHIR erőforrásokhoz. Az Enterprise Integration Pack a C-CDA FHIR erőforrásokhoz való leképezése kezeli.

    ![A logikai alkalmazás egészségügyi HL7 FHIR-rekord fogadása](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **EHR alkalmazás**: lekérdezése az Azure Cosmos DB FHIR adattár és a egy Service Bus-üzenetsorba küldött válasz mentése. A kód az GetNewOrModifiedFHIRDocuments alkalmazás nem éri el.

    ![A logikai alkalmazás segítségével Azure Cosmos DB lekérdezése](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Folyamat értesítés alkalmazás**: a törzsben elküldendő e-mail-értesítés a FHIR erőforrás dokumentumok.

    ![A logikai alkalmazás, amely a HL7 FHIR-erőforrással betegek e-mailt küld a törzskérelemben](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
A következő ábrán látható a betegek várólista. A címke tulajdonság értéke az e-mail tárgyát szolgál.

![A Service Bus-üzenetsor HL7 FHIR-oktatóanyagban használt](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>API-alkalmazás
API-alkalmazás csatlakozik az Azure Cosmos DB és a lekérdezések új vagy módosított FHIR dokumentumok erőforrástípus szerint. Az alkalmazás van egy vezérlő **FhirNotificationApi** egy művelettel **GetNewOrModifiedFhirDocuments**, lásd: [forrás API-alkalmazás](#api-app-source).

Használjuk a [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) az Azure Cosmos DB SQL .NET API osztályt. További információkért lásd: a [módosítási hírcsatorna cikk](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>GetNewOrModifiedFhirDocuments művelet

**bemenetek**
- DatabaseId
- CollectionId
- HL7 FHIR erőforrástípus neve
- Logikai érték: Start elejétől
- Int: Visszaadott dokumentumok száma

**Kimenetek**
- Sikeres: Állapotkód: 200, válasz: (JSON-tömböt) dokumentumok listája
- Hiba: Állapotkód: 404-es, válasz: "nem található"*erőforrás neve "* erőforrástípus"

<a id="api-app-source"></a>

**Forrás API-alkalmazás**

```csharp

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
                "from Last Run Date or Beginning of Collection creation"
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

Az alábbi képen láthatja, hogyan tesztelésére használt swagger a [FhirNotificationApi](#api-app-source).

![A Swagger-fájl, amellyel tesztelheti az API-alkalmazás](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Az Azure portal irányítópultján

Az alábbi képen látható a megoldás futtatása az Azure Portalon az Azure-szolgáltatások mindegyikét.

![Az Azure Portalon HL7 FHIR-oktatóanyagban használt összes szolgáltatásokat bemutató](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Összegzés

- Megtanulhatta, hogy az Azure Cosmos DB rendelkezik-e a natív támogatása az értesítések az új vagy módosított dokumentumok és mennyire egyszerű is használhatja. 
- A Logic Apps kihasználva munkafolyamatokat hozhat létre kód írása nélkül.
- Az Azure Service Bus-üzenetsorok használatával a terjesztési HL7 FHIR dokumentumok kezeléséhez.

## <a name="next-steps"></a>További lépések
Azure Cosmos DB kapcsolatos további információkért lásd: a [Azure Cosmos DB-kezdőlap](https://azure.microsoft.com/services/cosmos-db/). További információ a Logic Apps szolgáltatásról, tekintse meg a [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


