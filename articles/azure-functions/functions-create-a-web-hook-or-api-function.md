<properties
   pageTitle="Webhook vagy API Azure-függvény létrehozása | Microsoft Azure"
   description="Használja az Azure Functions szolgáltatást olyan függvények létrehozására, amelyeket WebHook vagy API-hívás hív meg."
   services="azure-functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/30/2016"
   ms.author="glenga"/>
   

# Webhook vagy API Azure-függvény létrehozása

Az Azure Functions egy eseményvezérelt, számítási igények szerint működtethető szolgáltatás, amelynek a segítségével ütemezett és aktivált kódegységek hozhatók létre, különböző programnyelveken megvalósítva. További információk az Azure Functions szolgáltatásról: [Azure Functions Overview](functions-overview.md) (Az Azure Storage áttekintése).

Ez a témakör bemutatja, hogyan hozható létre új Node.js függvény, amelyet egy GitHub-webhook hív meg. Az új függvény létrehozása egy, az Azure Functions portálon előre meghatározott sablon alapján történik. Az ehhez szükséges, a portálon elvégzendő lépésekről egy rövid videót is megtekinthet.

## Videó megtekintése

A következő videó bemutatja, hogyan végezheti el az ebben az oktatóprogramban található alapszintű lépéseket. 

[AZURE.VIDEO create-a-web-hook-or-api-azure-function]

##Webhook által aktivált függvény létrehozása sablonból

A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Ahhoz, hogy létrehozhassa a függvényt, aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/). 

1. Lépjen az [Azure Functions portálra](https://functions.azure.com/signin), és jelentkezzen be az Azure-fiókjával.

2. Ha rendelkezik meglévő függvényalkalmazással, amelyet használhat, válassza ki a **Your function apps** (Saját függvényalkalmazások) menüből, és kattintson az **Open** (Megnyitás) gombra. Új függvényalkalmazás létrehozásához adjon meg egy egyedi **nevet** az új függvényalkalmazásnak, vagy fogadja el az automatikusan létrehozott nevet, majd válassza ki a kívánt **régiót**, és kattintson a **Create + get started** (Létrehozás és első lépések) gombra. 

3. A függvényalkalmazásban, kattintson a **+ Új funkció** > **GitHub Webhook – Csomópont** > **Létrehozás** lehetőségre. Ezzel létrehoz egy alapértelmezett névvel rendelkező függvényt, amely a megadott sablonon alapul. 

    ![Új GitHub-webhook függvény létrehozása](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png) 

4. A **Fejlesztés** lapon tekintse meg a például szolgáló express.js függvényt a **Kód** ablakban. Ez a függvény GitHub-kérelmet fogad egy problémához fűzött megjegyzés webhookjától, naplózza a probléma szövegét, és a következőképpen kinéző választ küld a webhooknak: `New GitHub comment: <Your issue comment text>`.


    ![Új GitHub-webhook függvény létrehozása](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png) 

5. Másolja ki a **Function URL** (Függvény URL) és a **GitHub Secret** (GitHub-titok) értékét. Ezekre az értékekre szüksége lesz, amikor létrehozza a webhookot a GitHubban. 

6. Görgessen le a **Run** (Futtatás) elemhez, jegyezze fel a problémához fűzött megjegyzés előre meghatározott JSON-törzsét a kéréstörzsben, majd kattintson a **Run** (Futtatás) gombra. 
 
    Az új sablonalapú függvényt mindig tesztelheti a **Develop** (Fejlesztés) lapon, ha megad egy szükséges JSON-törzsadatot, majd a **Run** (Futtatás) gombra kattint. Ilyenkor a sablon előre meghatározott törzzsel rendelkezik a problémához fűzött megjegyzéshez. 
 
A következő lépésben a tényleges webhookot hozza létre a GitHub-tárban.

##A webhook konfigurálása

1. A GitHubban lépjen a tulajdonában álló adattárra; ez az összes elágaztatott adattárat tartalmazza.
 
2. Kattintson a **Settings** (Beállítások) > **Webhooks & services** (Webhookok és szolgáltatások) > **Add webhook** (Webhook hozzáadása) lehetőségre.

    ![Új GitHub-webhook függvény létrehozása](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)   

3. Illessze be a függvényt URL-jét és titkos kulcsát a **Payload URL** (Hasznos adat URL) és a **Secret** (Titkos kulcs) elembe, majd kattintson a **Let me select individual events** (Egyes események kiválasztása) gombra, válassza az **Issue comment** (Problémához fűzött megjegyzés) lehetőséget, és kattintson az **Add webhook** (Webhook hozzáadása) parancsra.

    ![Új GitHub-webhook függvény létrehozása](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png) 

Ezen a ponton a GitHub webhook arra van beállítva, hogy elindítsa a függvényt egy új, problémához fűzött megjegyzés hozzáadásakor.  
Eljött a tesztelés ideje.

##A függvény tesztelése

1. A GitHub-tárban nyissa meg az **Issues** (Problémák) lapot egy új böngészőablakban, kattintson a **New Issue** (Új probléma) elemre, adjon meg egy címet, majd kattintson a **Submit new issue** (Új probléma küldése) gombra. Meglévő problémát is megnyithat.

2. A problémában írjon be egy megjegyzést, majd kattintson a **Comment** (Megjegyzés) elemre. Ennél a pontnál visszaléphet az új webhookra a GitHubban, és a **Recent Deliveries** (Legutóbbi kézbesítések) alatt láthatja, hogy a webhook-kérelmet a rendszer elküldte, és a válasz törzse a következő: `New GitHub comment: <Your issue comment text>`.

3. A Functions portálon görgessen le a naplókhoz, és ellenőrizze, hogy a függvény aktiválódott-e, és a `New GitHub comment: <Your issue comment text>` kifejezés megtalálható-e a streamnaplókban.


##Következő lépések

A következő témakörökben további, az Azure Functions szolgáltatásra vonatkozó információkat talál.

+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
Programozói segédanyagok függvények kódolásához.
+ [Az Azure Functions tesztelése](functions-test-a-function.md)  
Különböző függvénytesztelési eszközöket és technikákat ír le.
+ [Az Azure Functions méretezése](functions-scale.md)  
Részletezi az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat, köztük a dinamikus szolgáltatáscsomagot, és segít a megfelelő csomag kiválasztásában.  


[AZURE.INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]



<!--HONumber=Sep16_HO4-->


