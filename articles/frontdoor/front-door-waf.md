---
title: Webalkalmazások gyors méretezése és biztosítása az Azure-beli előtérben és az Azure Web Application Firewall (WAF) használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használható a webalkalmazási tűzfal a Azure bejárati ajtó szolgáltatással
services: frontdoor
documentationcenter: ''
author: tremansdoerfer
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: rimansdo
ms.openlocfilehash: 6f91a98372aa85a52a6013a121235ca354004a84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743541"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Webalkalmazások gyors méretezése és biztosítása az Azure-előtérben és az Azure webalkalmazási tűzfal (WAF) használatával

Számos webalkalmazás a COVID-19-re vonatkozó legutóbbi hetekben gyors növekedést észlelt a forgalomban. Emellett ezek a webalkalmazások is megfigyelik a kártékony forgalmat, beleértve a szolgáltatásmegtagadási támadásokat is. Az ilyen igények kezelésének hatékony módja, a forgalom felskálázása és a támadások elleni védelem lehetővé tétele, hogy az Azure WAF gyorsított, gyorsítótárazási és biztonsági rétegként állítsa be a webalkalmazás előtt. Ez a cikk útmutatást nyújt arról, hogyan lehet gyorsan beolvasni ezt az Azure-előtérben az Azure WAF Setup használatával az Azure-ban vagy azon kívül futó webalkalmazásokhoz. 

Ebben az oktatóanyagban az Azure CLI használatával állíthatja be a WAF, de ezek a lépések teljes mértékben támogatottak Azure Portal, Azure PowerShell, Azure ARM és Azure REST API-k esetében is. 

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

Az ebben a blogban található utasítások az Azure parancssori felületét (CLI) használják. Tekintse meg ezt az útmutatót az [Azure CLI megkezdéséhez](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Tipp: könnyen & gyorsan megkezdheti az Azure CLI-vel való ismerkedést a [bash-ben Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Győződjön meg arról, hogy az előtérben lévő bővítmény hozzá van adva az Azure CLI-hez

```azurecli-interactive 
az extension add --name front-door
```

Megjegyzés: az alábbi parancsokkal kapcsolatos további információkért tekintse meg az [Azure CLI-referenciáját az előtérben](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>1. lépés: Azure bejárati ajtó (AFD) erőforrásának létrehozása


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--háttér-címe**: a háttér címe a védelemmel ellátni kívánt alkalmazás teljes TARTOMÁNYNEVE (FQDN) neve. Például: myapplication.contoso.com

**--elfogadva-protokollok**: az elfogadott protokollok határozzák meg, hogy a AFD hány protokollt kíván támogatni a webalkalmazások támogatásához. Ilyen például a--Accepted-Protocols HTTP HTTPS.

**--Name (név**): adja meg a AFD-erőforrás nevét

**--Resource-Group**: az az erőforráscsoport, amelyben ezt a AFD-erőforrást be kívánja helyezni.  Az erőforráscsoportok részletes ismertetését az erőforráscsoportok kezelése az Azure-ban című témakörben találja meg.

A parancs sikeres végrehajtása után kapott válaszban keresse meg a "hostName" kulcsot, és jegyezze fel az értékét, amelyet egy későbbi lépésben használni fog. Az állomásnév a létrehozott AFD-erőforrás DNS-neve.

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>2. lépés: Azure-beli WAF-profil létrehozása az Azure-beli előtérben lévő erőforrásokkal való használathoz

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--Name adja meg az Azure WAF-szabályzat nevét

--Resource-Group az az erőforráscsoport, amelyben ezt a WAF-erőforrást be kívánja helyezni. 

A fenti CLI-kód egy WAF házirendet hoz létre, amely engedélyezve van, és a megelőzési módban van. 

Megjegyzés: Előfordulhat, hogy a WAF is létre kell hoznia észlelési módban, és meg kell figyelnie, hogy miként észleli & a kártékony kéréseket (és nem blokkolja), mielőtt a védelmi módra vált.

A parancs sikeres végrehajtása után kapott válaszban keresse meg az "ID" kulcsot, és jegyezze fel az értékét, amelyet egy későbbi lépésben használni fog. Az azonosító mezőnek formátumban kell lennie

/Subscriptions/**előfizetés azonosítója**/resourcegroups/**erőforráscsoport neve**/Providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF házirend neve**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>3. lépés: felügyelt szabályrendszerek hozzáadása ehhez a WAF-házirendhez

Egy WAF szabályzatban hozzáadhat olyan felügyelt szabályrendszerek, amely a Microsoft által készített és felügyelt szabályok halmaza, és a teljes veszélyforrások elleni védelmet nyújt a jelölőnégyzetből. Ebben a példában két olyan szabályrendszerek (1) adunk hozzá, amely védelmet nyújt a gyakori webes fenyegetésekkel szemben, és (2) bot Protection szabályrendszert, amely védelmet biztosít a rosszindulatú robotok ellen

(1) az alapértelmezett szabályrendszert hozzáadása

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) a robot-kezelői szabályrendszert hozzáadása

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--Policy-nevezze el az Azure WAF-erőforráshoz megadott nevet

--Resource-Group az az erőforráscsoport, amelyet a WAF-erőforrásba helyezett.

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>4. lépés: a WAF szabályzat hozzárendelése a AFD-erőforráshoz

Ebben a lépésben társítjuk a WAF szabályzatot, amely a webalkalmazás előtt található AFD-erőforrással lett létrehozva.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--nevezze el a AFD-erőforráshoz megadott nevet

--Resource-Group az az erőforráscsoport, amelyben az Azure-beli bejárati erőforrást helyezte.

– Itt állíthatja be, hogy a AFD-erőforráshoz társított frontendEndpoint az újonnan létrehozott WAF-szabályzattal hogyan frissítse a WebApplicationFirewallPolicyLink attribútumot. A WAF szabályzat azonosítója a fenti #2 lépéstől kapott válaszban található.

Megjegyzés: a fenti példa arra az esetre mutat, ha nem egyéni tartományt használ, ha

Ha nem használ egyéni tartományokat a webalkalmazásokhoz való hozzáféréshez, kihagyhatja a következő lépést: #5. Ebben az esetben a végfelhasználóknak a webalkalmazáshoz való kapcsolódáshoz a #1 lépésben beszerzett állomásnevet fogja biztosítani

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>5. lépés: egyéni tartomány konfigurálása a webalkalmazáshoz

Kezdetben a webalkalmazás egyéni tartományneve (amelyet az ügyfelek az alkalmazásra hivatkoznak, például www.contoso.com) arra a helyre mutattak, ahol a AFD bevezetése előtt futott. Az architektúra ezen változása után a AFD + WAF hozzáadásával az alkalmazás előtt az adott egyéni tartománynak megfelelő DNS-bejegyzésnek most erre a AFD-erőforrásra kell mutatnia. Ezt úgy teheti meg, hogy a DNS-kiszolgálón lévő bejegyzést újraképezi a AFD állomásnévvel, amelyet a #1 lépésben észlelt.

A DNS-rekordok frissítésének konkrét lépései a DNS-szolgáltatótól függenek, de ha Azure DNSt használ a DNS-név üzemeltetéséhez, tekintse meg a [DNS-rekord frissítésére](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) és a AFD Állomásnévre mutató lépéseket a dokumentációban. 

Itt az egyik legfontosabb dolog, hogy ha szüksége van a felhasználók számára, hogy a zóna csúcsán keresztül navigáljon a webhelyhez, akkor a példában, a contoso.com Azure DNS és az [alias bejegyzéstípust](https://docs.microsoft.com/azure/dns/dns-alias) kell használnia a DNS-név üzemeltetéséhez. 

Emellett frissítenie kell a AFD-konfigurációt az [egyéni tartomány hozzáadásához](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) , hogy a AFD megértse ezt a leképezést.

Végül, ha egyéni tartományt használ a webalkalmazás eléréséhez, és engedélyezni szeretné a HTTPS protokollt, rendelkeznie kell az [egyéni tartomány beállításához tartozó tanúsítványokkal a AFD-ben](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="step-6-lock-down-your-web-application"></a>6. lépés: a webalkalmazás zárolása

Az egyik választható ajánlott eljárás az, hogy csak a AFD élek kommunikáljanak a webalkalmazással. Ez a művelet gondoskodik arról, hogy senki ne kerüljék el a AFD-védelmet, és közvetlenül hozzáférjenek az alkalmazásaihoz. Ezt a zárolást úgy érheti el, ha meglátogatja a [AFD GYIK szakaszát](https://docs.microsoft.com/azure/frontdoor/front-door-faq) , és rámutatott arra a kérdésre, hogy csak a AFD fér hozzá a hozzáférési háttérrendszer-zároláshoz.
