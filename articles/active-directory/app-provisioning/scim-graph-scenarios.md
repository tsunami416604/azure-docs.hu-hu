---
title: Az SCIM, a Microsoft Graph és az Azure AD-kiépítési szolgáltatás használata a felhasználók kibővítéséhez és az alkalmazás bővítéséhez a szükséges adatokkal | Microsoft dokumentumok
description: Az SCIM és a Microsoft Graph együttes használata a felhasználók kiépítéséhez és az alkalmazás bővítéséhez a szükséges adatokkal.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087620"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Az SCIM és a Microsoft Graph együttes használata a felhasználók kiépítéséhez és az alkalmazás bővítéséhez a szükséges adatokkal

**Célközönség:** Ez a dokumentum az Azure AD-vel integrált alkalmazásokat fejlesztő fejlesztőkszámára készült. Mások számára szeretnének integrálni egy meglévő alkalmazás, mint a Zoom, ServiceNow, és a DropBox akkor hagyja ki ezt, és tekintse át az alkalmazás specifikus [oktatóanyagok](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 

**Gyakori helyzetek**

> [!div class="checklist"]
> * Felhasználók automatikus létrehozása az alkalmazásban
> * A felhasználók automatikus eltávolítása az alkalmazásból, ha már nem kellene hozzáférésük
> * Az alkalmazás integrálása több identitásszolgáltatóval a kiépítéshez
> * Az alkalmazás richdrészea Microsoft-szolgáltatásokból, például a SharePointból, az Outlookból és az Office-ból származó adatokkal gazdagíthatja az alkalmazást.
> * Felhasználók és csoportok automatikus létrehozása, frissítése és törlése az Azure AD-ben és az Active Directoryban

![SciM Graph döntési fa](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>1. eset: Felhasználók automatikus létrehozása az alkalmazásban
Ma, informatikai rendszergazdák manuálisan létre felhasználói fiókok az én alkalmazás minden alkalommal, amikor valakinek szüksége van a hozzáféréshez, vagy rendszeresen feltölteni CSV fájlokat. A folyamat időigényes az ügyfelek számára, és lassítja az alkalmazásom elfogadását. Minden amire szükségem van van alapvető [felhasználó](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) információ mint név, elektronikus levél, és userPrincipalName -hoz teremt egy felhasználó. Továbbá az ügyfeleim különböző azonosítókat használnak, és nem rendelkezem az erőforrásokkal a szinkronizálási motor és az egyéni integrációk fenntartásához az egyes IdP-vel. 

**Javaslat**: Scim-kompatibilis [/Felhasználók](https://aka.ms/scimreferencecode) végpont támogatása. Az ügyfelek könnyen használhatják ezt a végpontot az Azure AD-kiépítési szolgáltatással való integrációhoz, és automatikusan létrehozhatnak felhasználói fiókokat, amikor hozzáférésre van szükségük. A végpontot egyszer is létrehozhatja, és kompatibilis lesz az összes idp-vel, anélkül, hogy szinkronmotort kellene fenntartania. Tekintse meg az alábbi példakérelmet a felhasználó létrehozásának módjáról.

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>2. eset: A felhasználók automatikus eltávolítása az alkalmazásból
Az alkalmazásom használó ügyfelei biztonságra összpontosítanak, és cégirányítási követelményekkel rendelkeznek a fiókok eltávolításához, amikor az alkalmazottaknak már nincs rájuk szükségük. Hogyan automatizálhatom az alkalmazásból való megszüntetést?

**Ajánlás:** SciM-kompatibilis /Users végpont támogatása. Az Azure AD-kiépítési szolgáltatás letiltási és törlési kérelmeket küld, ha a felhasználónak már nem kell hozzáféréssel rendelkeznie. Javasoljuk, hogy a felhasználók letiltását és törlését is támogassa. Tekintse meg az alábbi példákat, hogy hogyan néz ki a letiltási és törlési kérelem. 

Felhasználó letiltása
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Felhasználó törlése
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>3. forgatókönyv: Csoporttagságok kezelésének automatizálása az alkalmazásomban
Az alkalmazás csoportokra támaszkodik a különböző erőforrásokhoz való hozzáféréshez, és az ügyfelek szeretnék újra felhasználni az Azure AD-ben lévő csoportokat. Hogyan importálhatok csoportokat az Azure AD-ből, és hogyan frissíthetem őket a tagságok változásával?  

**Ajánlás:** SciM-kompatibilis /Csoportok [végpont](https://aka.ms/scimreferencecode)támogatása . Az Azure AD-kiépítési szolgáltatás gondoskodik a csoportok létrehozásáról és a tagsági frissítések kezeléséről az alkalmazásban. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>4. forgatókönyv: Az alkalmazás omlása a Microsoft-szolgáltatásokból, például a Teamsből, az Outlookból és a OneDrive-ból származó adatokkal.
Az alkalmazásom be van építve a Microsoft Teamsbe, és az üzenetadatokra támaszkodik. Ezenkívül a felhasználók számára fájlokat tárolunk a OneDrive-on. Hogyan gazdagíthatom az alkalmazásomat az ezekből a szolgáltatásokból és a Microsofttól származó adatokkal?

**Ajánlás:** A [Microsoft Graph](https://docs.microsoft.com/graph/) a Belépési pont a Microsoft-adatok eléréséhez. Minden számítási feladatok elérhetővé teszi kita az API-k at a szükséges adatokkal. A Microsoft graph használható együtt [SCIM kiépítése](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) a fenti forgatókönyvek. Az SCIM használatával alapvető felhasználói attribútumokat helyezhet el az alkalmazásba, miközben a graph-ba hívás közben behívhat bármilyen más szükséges adatot. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>5. forgatókönyv: Kövesse nyomon a változásokat a Microsoft-szolgáltatások, például a Teams, az Outlook és az Azure AD.
Képesnek kell lennem nyomon követni a Teams és az Outlook üzenetek változásait, és valós időben kell reagálnom rájuk. Hogyan érhetem el ezeket a módosításokat az alkalmazásomba?

**Ajánlás:** A Microsoft Graph [változási értesítéseket](https://docs.microsoft.com/graph/webhooks) és változáskövetést biztosít a különböző erőforrásokhoz. Vegye figyelembe a változásértesítések következő korlátait:
- Ha egy esemény fogadója tudomásul vesz egy eseményt, de bármilyen okból nem cselekszik, az esemény elveszhet
- Ha egy esemény fogadója tudomásul vesz egy eseményt, de bármilyen okból nem cselekszik, az esemény elveszhet
- A változási értesítések nem mindig tartalmazzák az [erőforrásadatokat](https://docs.microsoft.com/graph/webhooks-with-resource-data) A fenti okok miatt a fejlesztők gyakran használnak változásértesítéseket a változáskövetéslel együtt a szinkronizálási forgatókönyvekhez. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>6. forgatókönyv: Felhasználók és csoportok kiépítése az Azure AD-ben.
Az alkalmazásom olyan felhasználóadatait hozza létre, amelynek az ügyfeleknek szükségük van az Azure AD-ben. Ez lehet egy HR-alkalmazás, mint kezeli a bérbeadás, egy kommunikációs alkalmazás, amely telefonszámokat hoz létre a felhasználók számára, vagy más alkalmazás, amely olyan adatokat hoz létre, amelyek értékesek lennének az Azure AD-ben. Hogyan tudom feltölteni a felhasználói rekordot az Azure AD-ben ezekkel az adatokkal? 

**Ajánlás** A Microsoft graph elérhetővé teszi a /Users és a /Groups végpontokat, amelyekkel ma integrálható a felhasználók Azure AD-be való kiépítéséhez. Vegye figyelembe, hogy az Azure Active Directory nem támogatja a felhasználók visszaírását az Active Directoryba. 

> [!NOTE]
> A Microsoft rendelkezik egy kiépítési szolgáltatással, amely lekéri az adatokat a HR-alkalmazásokból, például a Workday és a SuccessFactors alkalmazásokból. Ezeket az integrációkat a Microsoft építi és kezeli. A bevezetés egy új HR alkalmazás a szolgáltatás, akkor kérheti, hogy a [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Kapcsolódó cikkek

- [A Microsoft Graph szinkronizálási dokumentációjának áttekintése](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Egyéni SCIM-alkalmazások integrálása az Azure AD-vel](use-scim-to-provision-users-and-groups.md)
