---
title: "Webhook vagy API Azure-függvény létrehozása | Microsoft Docs"
description: "Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvények létrehozására, amelyeket WebHook vagy API-hívás hív meg."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: a8f6d111a010666bf4aaaf05e061381cc8fffed0
ms.openlocfilehash: 23a65319fe1825e2ba51f2fd5a2d0b65ca499472


---
# <a name="create-a-webhook-or-api-azure-function"></a>Webhook vagy API Azure-függvény létrehozása
Az Azure Functions egy eseményvezérelt, számítási igények szerint működtethető szolgáltatás, amelynek a segítségével ütemezett és aktivált kódegységek hozhatók létre, különböző programnyelveken megvalósítva. További információk az Azure Functions szolgáltatásról: [Azure Functions Overview](functions-overview.md) (Az Azure Storage áttekintése).

Ez a témakör bemutatja, hogyan hozhatók létre olyan JavaScript-függvények, amelyeket egy GitHub-webhook hív meg. Az új függvény létrehozása egy, az Azure Functions portálon előre meghatározott sablon alapján történik. Az ehhez szükséges, a portálon elvégzendő lépésekről egy rövid videót is megtekinthet.

A jelen oktatóanyag általános lépései egy adott függvény JavaScript helyett C# vagy F# nyelven történő létrehozásához is használhatók. 

## <a name="watch-the-video"></a>Videó megtekintése
A következő videó bemutatja, hogyan végezheti el az ebben az oktatóprogramban található alapszintű lépéseket. 

>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-a-Web-Hook-or-API-Azure-Function/player]
>
>

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

+ Aktív Azure-fiók. Ha még nincs fiókja, [regisztrálhat egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).  
 A [függvények kipróbálását](https://functions.azure.com/try) lehetővé tévő webhelyen az oktatóanyagot Azure-fiók nélkül is elvégezheti.
+ Egy GitHub-fiók. Ha még nincs fiókja, [regisztrálhat egy ingyenes GitHub-fiókra](https://github.com/join). 

## <a name="create-a-webhook-triggered-function-from-the-template"></a>Webhook által aktivált függvény létrehozása sablonból
A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. 

1. Lépjen az [Azure Functions portálra](https://functions.azure.com/signin), és jelentkezzen be az Azure-fiókjával.

2. Ha rendelkezik meglévő függvényalkalmazással, amelyet használhat, válassza ki a **Your function apps** (Saját függvényalkalmazások) menüből, és kattintson az **Open** (Megnyitás) gombra. Függvényalkalmazás létrehozásához adjon meg egy egyedi **nevet** az új függvényalkalmazásnak, vagy fogadja el az automatikusan létrehozott nevet, majd válassza ki a kívánt **régiót**, és kattintson a **Create + get started** (Létrehozás és első lépések) gombra. 

3. A függvényalkalmazásban kattintson a **+ Új funkció** > **GitHub Webhook – JavaScript** > **Létrehozás** lehetőségre. Ezzel a lépéssel létrehoz egy alapértelmezett névvel rendelkező függvényt, amely a megadott sablonon alapul. Függvényeket C# vagy F# nyelven is létrehozhat.
   
    ![GitHub-webhook által aktivált függvény létrehozása](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png) 

4. A **Fejlesztés** lapon tekintse meg a például szolgáló express.js függvényt a **Kód** ablakban. Ez a függvény GitHub-kérelmet fogad egy problémához fűzött megjegyzés webhookjától, naplózza a probléma szövegét, és a következőképpen kinéző választ küld a webhooknak: `New GitHub comment: <Your issue comment text>`.

    ![A függvénykód áttekintése](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png) 

1. Másolja ki és mentse el a **Function URL** (Függvény URL) és a **GitHub Secret** (GitHub-titok) értékét. Ezekre az értékekre a következő szakaszban, a GitHub-webhook konfigurálásához lesz szükség. 

2. Kattintson a **Test** (Tesztelés) elemre, jegyezze fel a problémához fűzött megjegyzés előre meghatározott JSON-törzsét a **kéréstörzsben**, majd kattintson a **Run** (Futtatás) gombra. 

    ![A webhook függvény tesztelése a portálon](./media/functions-create-a-web-hook-or-api-function/functions-test-webhook-in-portal.png)
   
    > [!NOTE]
    > Az új sablonalapú függvényt mindig tesztelheti a **Develop** (Fejlesztés) lapon, ha megad egy szükséges JSON-törzsadatot, majd a **Run** (Futtatás) gombra kattint. Ilyenkor a sablon előre meghatározott törzzsel rendelkezik a problémához fűzött megjegyzéshez. 

A következő lépésben a tényleges webhookot hozza létre a GitHub-tárban.

## <a name="configure-the-webhook"></a>A webhook konfigurálása
1. A GitHubban lépjen a tulajdonában álló adattárra. Használhat bármely elágaztatott adattárat is.
 
2. Kattintson a **Settings** (Beállítások) > **Webhooks & services** (Webhookok és szolgáltatások) > **Add webhook** (Webhook hozzáadása) lehetőségre.
   
    ![GitHub-webhook hozzáadása](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)   

3. Illessze be a függvény URL-jét és titkos kulcsát a **Payload URL** (Hasznos adat URL) és a **Secret** (Titkos kulcs) mezőkbe, majd a **Content type** (Tartalom típusa) elemnél adja meg az **application/json** (alkalmazás/json) értéket.

4. Kattintson a **Let me select individual events** (Egyes események kiválasztása) gombra, válassza az **Issue comment** (Problémához fűzött megjegyzés) lehetőséget, majd kattintson az **Add webhook** (Webhook hozzáadása) parancsra.
   
    ![A webhook URL-címének és titkos kulcsának beállítása](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png) 

Ezen a ponton a GitHub webhook arra van beállítva, hogy elindítsa a függvényt egy új, problémához fűzött megjegyzés hozzáadásakor.  
Eljött a tesztelés ideje.

## <a name="test-the-function"></a>A függvény tesztelése
1. A GitHub-tárban nyissa meg az **Issues** (Problémák) lapot egy új böngészőablakban.

2. Az új ablakban kattintson a **New Issue** (Új probléma) elemre, adjon meg egy címet, majd kattintson a **Submit new issue** (Új probléma küldése) gombra. Meglévő problémát is megnyithat.

2. A problémában írjon be egy megjegyzést, majd kattintson a **Comment** (Megjegyzés) elemre. 

3. A másik GitHub-ablakban kattintson az új webhook melletti **Edit** (Szerkesztés) lehetőségre, görgessen lefelé a **Recent Deliveries** (Legutóbbi kézbesítések) pontig, és itt ellenőrizze, hogy a webhook-kérelmet a rendszer elküldte-e, és a válasz törzse a következő-e: `New GitHub comment: <Your issue comment text>`.

3. A Functions portálon görgessen le a naplókhoz, és ellenőrizze, hogy a függvény aktiválódott-e, és a `New GitHub comment: <Your issue comment text>` kifejezés megtalálható-e a streamnaplókban.

## <a name="next-steps"></a>Következő lépések
A következő témakörökben további, az Azure Functions szolgáltatásra vonatkozó információkat talál.

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához.
* [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.
* [Az Azure Functions méretezése](functions-scale.md)  
  Az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat ismerteti, köztük a Használatalapú futtatási csomagot, és segít a megfelelő csomag kiválasztásában.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO1-->


