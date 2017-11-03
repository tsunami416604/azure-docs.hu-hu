---
title: "Az Azure Mobile Engagement - háttér-integráció"
description: "Csatlakozás az Azure Mobile Engagement egy SharePoint-háttérrendszerrel SharePoint kampányok létrehozásához"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d49f1094f4c3f170f3618f3e19e42266f9ae8858
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---api-integration"></a>Az Azure Mobile Engagement - API-integráció
Egy automatizált marketing rendszerben létrehozása és aktiválása, a marketingkampányok is automatikusan történik. Erre a célra - Azure Mobile Engagement lehetővé teszi, hogy az ilyen API-k használatával is automatizált marketingkampányok létrehozása. 

Általában az ügyfelek a Mobile Engagement előtér illesztő segítségével a marketingkampányok részeként hozza létre a közlemények/szavazások stb. Azonban a marketingkampányok egyre érett, szükség van az adatokat, hogy egy teljesen automatizált folyamat is létrehozható, amely kampányok hoz létre a Mobile Engagement zárolva van (például a CRM vagy CMS rendszer például SharePoint) a háttérrendszerek ki dinamikusan áramlanak a háttérrendszerek adatok alapján. 

![][5]

Ez az oktatóanyag egy olyan forgatókönyvet, ahol a SharePoint üzleti felhasználók és tölti fel az értékesítési adatokat egy SharePoint-lista egy automatikus folyamat szerzi be a listában szereplő elemeket, és csatlakozik a Mobile Engagement rendszer használata a rendelkezésre álló REST API-k létrehozásához az végig kell vinnie a a SharePoint-adatokból marketingkampányt. 

> [!IMPORTANT]
> Általában használhatja ezt a mintát kiindulási pontként a Mobile Engagement REST API-k előhívhat, hogy az adatokat a két fő szempontjait hívja az API - hitelesítő és tompított paraméterek alapos ismerete. 
> 
> 

## <a name="sharepoint-integration"></a>SharePoint-integráció
1. Ez a minta SharePoint-lista néz. **Cím**, **kategória**, **NotificationTitle**, **üzenet** és **URL-cím** a hirdetmény létrehozásához használt. Nincs oszlopát **IsProcessed** konzol program formájában a minta automation folyamat által használt. Akkor is fut a konzol, az Azure webjobs-feladat program, így is ütemezheti, vagy használhatja a SharePoint-munkafolyamat létrehozása és aktiválása a közlemény, amikor egy elem bekerülnek a SharePoint-lista program közvetlenül. Ez a példa a konzol programot, amely a SharePoint elemeinek listában, és bejelentés létrehozása az Azure Mobile Engagement esetében kerül, és megjelöli végül használjuk a **IsProcessed** jelző IGAZ értékű, a sikeres bejelentés létrehozása.
   
    ![][1]
2. A kód a minta használjuk *távoli hitelesítés a SharePoint Online használata az ügyfél objektummodell* [Itt](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) való hitelesítéshez szükséges a SharePoint-listán.
3. Hitelesítését követően azt ismétlése minden újonnan létrehozott elemek megállapítása a listaelemek (amelyeket lesz **IsProcessed** = false). 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Mobile Engagement-integráció
1. Amint egy elem feldolgozása - igénylő találtunk azt bontsa ki a közlemény létrehozása a listaelemek és hívás szükséges adatokat `CreateAzMECampaign` létrehozták, és ezt követően `ActivateAzMECampaign` aktiválásához. Ezek a lényegében REST API-hívásokat a Mobile Engagement háttérrendszeréhez hívása. 
2. A Mobile Engagement REST API-k szükséges egy **alapszintű hitelesítési séma engedélyezési HTTP-fejléc** amely áll a `ApplicationId` és a `ApiKey` amely kap az Azure-portálon. Győződjön meg arról, hogy a kulcsot használ a **api-kulcsokat** szakasz és *nem* a a **sdk kulcsok** szakasz. 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. A hirdetmény létrehozásához írja be a kampány – tekintse meg a [dokumentáció](https://msdn.microsoft.com/library/azure/mt683750.aspx). Győződjön meg arról, hogy a kampány ad meg kell `kind` , *közlemény* és a [hasznos](https://msdn.microsoft.com/library/azure/mt683751.aspx) és FormUrlEncodedContent, átadja azt. 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. Ha már létrehozott bejelentés, megjelenik a következőhöz a Mobile Engagement portál (vegye figyelembe, hogy az állapot = Vázlat és aktív = N/A)
   
    ![][3]
5. `CreateAzMECampaign`létrehoz egy bejelentési kampányt, és visszaadja az azonosítója a hívó. `ActivateAzMECampaign`Ez az azonosító szükséges aktiválja a kampányt paraméterként. 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. Ha már aktiválta a közlemény, a Mobile Engagement portálon megjelenik a következőhöz:
   
    ![][4]
7. Amint az a kampány aktiválása lekérdezi azokat az eszközöket, amelyek megfelelnek a feltétel a kampány indul el értesítések jelent. 
8. Is láthatja, hogy a listaelem jelölésű IsProcessed = hamis értéket Igaz értékre lett állítva a közlemény kampány létrehozása után.  

Ez a minta legtöbbször a szükséges tulajdonságok megadása egyszerű közlemény a kampány létrehozása. Testre szabhatja ezt, mint amennyit a portálról szereplő információk segítségével is [Itt](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png



