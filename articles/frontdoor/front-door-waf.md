---
title: Webalkalmazások gyors méretezése és biztosítása az Azure-beli előtérben és az Azure Web Application Firewall (WAF) használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a webalkalmazási tűzfalat az Azure bejárati ajtó szolgáltatásával
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 1958481193b66c8cec2cb6a1ac6648a6900d70ac
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531202"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Oktatóanyag: webalkalmazások gyors méretezése és biztosítása az Azure-beli előtérben és az Azure Web Application Firewall (WAF) használatával

Számos webalkalmazás a COVID-19-re vonatkozó legutóbbi hetekben gyors növekedést észlelt a forgalomban. Emellett ezek a webalkalmazások is megfigyelik a kártékony forgalmat, beleértve a szolgáltatásmegtagadási támadásokat is. Az ilyen igények kezelésének hatékony módja, a forgalom felskálázása és a támadások elleni védelem lehetővé tétele, hogy az Azure WAF gyorsított, gyorsítótárazási és biztonsági rétegként állítsa be a webalkalmazás előtt. Ez a cikk útmutatást nyújt arról, hogyan lehet gyorsan beolvasni ezt az Azure-előtérben az Azure WAF Setup használatával az Azure-ban vagy azon kívül futó webalkalmazásokhoz. 

Ebben az oktatóanyagban az Azure CLI használatával állíthatja be a WAF, de ezek a lépések teljes mértékben támogatottak Azure Portal, Azure PowerShell, Azure ARM és Azure REST API-k esetében is. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Hozzon létre egy bejárati ajtót.
> - Hozzon létre egy Azure WAF szabályzatot.
> - Szabályrendszerek konfigurálása a WAF-házirendhez.
> - WAF-házirend hozzárendelése a bejárati ajtóhoz
> - Egyéni tartomány konfigurálása

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az ebben a blogban található utasítások az Azure parancssori felületét (CLI) használják. Tekintse meg ezt az útmutatót az [Azure CLI megkezdéséhez](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Tipp: könnyen & gyorsan megkezdheti az Azure CLI-vel való ismerkedést a [bash-ben Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Győződjön meg arról, hogy az előtérben lévő bővítmény hozzá van adva az Azure CLI-hez

```azurecli-interactive 
az extension add --name front-door
```

Megjegyzés: az alábbi parancsokkal kapcsolatos további információkért tekintse meg az [Azure CLI-referenciáját az előtérben](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="create-an-azure-front-door-afd-resource"></a>Azure bejárati ajtó (AFD) erőforrásának létrehozása

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--háttér-címe**: a háttér címe a védelemmel ellátni kívánt alkalmazás teljes TARTOMÁNYNEVE (FQDN) neve. Például: myapplication.contoso.com

**--elfogadva-protokollok**: az elfogadott protokollok határozzák meg, hogy a AFD hány protokollt kíván támogatni a webalkalmazások támogatásához. Ilyen például a--Accepted-Protocols HTTP HTTPS.

**--Name (név**): adja meg a AFD-erőforrás nevét

**--Resource-Group**: az az erőforráscsoport, amelyben ezt a AFD-erőforrást be kívánja helyezni.  Az erőforráscsoportok részletes ismertetését az erőforráscsoportok kezelése az Azure-ban című témakörben találja meg.

A parancs sikeres végrehajtása után kapott válaszban keresse meg a "hostName" kulcsot, és jegyezze fel az értékét, amelyet egy későbbi lépésben használni fog. Az állomásnév a létrehozott AFD-erőforrás DNS-neve.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Azure-beli WAF-profil létrehozása az Azure-beli előtérben lévő erőforrásokkal való használathoz

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--Name adja meg az Azure WAF-szabályzat nevét

--Resource-Group az az erőforráscsoport, amelyben ezt a WAF-erőforrást be kívánja helyezni. 

A fenti CLI-kód egy WAF házirendet hoz létre, amely engedélyezve van, és a megelőzési módban van. 

Megjegyzés: Előfordulhat, hogy a WAF is létre kell hoznia észlelési módban, és meg kell figyelnie, hogy miként észleli & a kártékony kéréseket (és nem blokkolja), mielőtt a védelmi módra vált.

A parancs sikeres végrehajtása után kapott válaszban keresse meg az "ID" kulcsot, és jegyezze fel az értékét, amelyet egy későbbi lépésben használni fog. Az azonosító mezőnek formátumban kell lennie

/Subscriptions/**előfizetés azonosítója**/resourcegroups/**erőforráscsoport neve**/Providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF házirend neve**

## <a name="add-managed-rulesets-to-this-waf-policy"></a>Felügyelt szabályrendszerek hozzáadása ehhez a WAF-házirendhez

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

## <a name="associate-the-waf-policy-with-the-afd-resource"></a>A WAF szabályzat hozzárendelése a AFD-erőforráshoz

Ebben a lépésben társítjuk a WAF szabályzatot, amely a webalkalmazás előtt található AFD-erőforrással lett létrehozva.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--nevezze el a AFD-erőforráshoz megadott nevet

--Resource-Group az az erőforráscsoport, amelyben az Azure-beli bejárati erőforrást helyezte.

– Itt állíthatja be, hogy a AFD-erőforráshoz társított frontendEndpoint az újonnan létrehozott WAF-szabályzattal hogyan frissítse a WebApplicationFirewallPolicyLink attribútumot. A WAF szabályzat azonosítója a fenti #2 lépéstől kapott válaszban található.

Megjegyzés: a fenti példa arra az esetre mutat, ha nem egyéni tartományt használ, ha

Ha nem használ egyéni tartományokat a webalkalmazásokhoz való hozzáféréshez, kihagyhatja a következő lépést: #5. Ebben az esetben a végfelhasználóknak a webalkalmazáshoz való kapcsolódáshoz a #1 lépésben beszerzett állomásnevet fogja biztosítani

## <a name="configure-custom-domain-for-your-web-application"></a>Egyéni tartomány konfigurálása a webalkalmazáshoz

Kezdetben a webalkalmazás egyéni tartományneve (amelyet az ügyfelek az alkalmazásra hivatkoznak, például www.contoso.com) arra a helyre mutattak, ahol a AFD bevezetése előtt futott. Az architektúra ezen változása után a AFD + WAF hozzáadásával az alkalmazás előtt az adott egyéni tartománynak megfelelő DNS-bejegyzésnek most erre a AFD-erőforrásra kell mutatnia. Ezt úgy teheti meg, hogy a DNS-kiszolgálón lévő bejegyzést újraképezi a AFD állomásnévvel, amelyet a #1 lépésben észlelt.

A DNS-rekordok frissítésének konkrét lépései a DNS-szolgáltatótól függenek, de ha Azure DNSt használ a DNS-név üzemeltetéséhez, tekintse meg a [DNS-rekord frissítésére](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) és a AFD Állomásnévre mutató lépéseket a dokumentációban. 

Itt az egyik legfontosabb dolog, hogy ha szüksége van a felhasználók számára, hogy a zóna csúcsán keresztül navigáljon a webhelyhez, akkor például a contoso.com-t kell használnia, Azure DNS és az [alias bejegyzéstípust](https://docs.microsoft.com/azure/dns/dns-alias) kell használni a DNS-név tárolásához. 

Emellett frissítenie kell a AFD-konfigurációt az [egyéni tartomány hozzáadásához](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) , hogy a AFD megértse ezt a leképezést.

Végül, ha egyéni tartományt használ a webalkalmazás eléréséhez, és engedélyezni szeretné a HTTPS protokollt, rendelkeznie kell az [egyéni tartomány beállításához tartozó tanúsítványokkal a AFD-ben](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="lock-down-your-web-application"></a>A webalkalmazás zárolása

Az egyik választható ajánlott eljárás az, hogy csak a AFD élek kommunikáljanak a webalkalmazással. Ez a művelet gondoskodik arról, hogy senki ne kerüljék el a AFD-védelmet, és közvetlenül hozzáférjenek az alkalmazásaihoz. Ezt a zárolást úgy érheti el, ha meglátogatja a [AFD GYIK szakaszát](https://docs.microsoft.com/azure/frontdoor/front-door-faq) , és rámutatott arra a kérdésre, hogy csak a AFD fér hozzá a hozzáférési háttérrendszer-zároláshoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az oktatóanyag erőforrásaira, az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítsa el az erőforráscsoportot, a bejárati ajtót és a WAF házirendet.

```azurecli-interactive
  az group delete \
    --name <>
```
– nevezze el az oktatóanyagban üzembe helyezett összes erőforrás erőforráscsoport-nevét.

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan lehet elhárítani a bejárati ajtót, folytassa a útmutatók útmutatását.

> [!div class="nextstepaction"]
> [Gyakori útválasztási problémák elhárítása](front-door-troubleshoot-routing.md)
