---
title: Webalkalmazások méretezése és biztosítása az Azure bejárati ajtó és a WAF használatával
description: Ez az oktatóanyag bemutatja, hogyan használhatja az Azure-webalkalmazási tűzfalat az Azure bevezető ajtó szolgáltatásával.
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
ms.openlocfilehash: 2d531289a1d6e8c484b0334e570d943acdb82268
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276254"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Oktatóanyag: webalkalmazások gyors méretezése és biztosítása az Azure-beli előtérben és az Azure webalkalmazási tűzfal (WAF) használatával

A COVID-19 miatt számos webalkalmazás gyors növekedést észlelt az elmúlt hetekben. Ezek a webalkalmazások a rosszindulatú adatforgalmat is megtapasztalják, beleértve a szolgáltatásmegtagadási támadásokat is. A forgalom felskálázása és a támadások elleni védelem hatékony módja: az Azure WAF a webalkalmazás előtt gyorsított, gyorsítótárazási és biztonsági rétegként állítható be. Ez a cikk útmutatást nyújt arról, hogyan lehet gyorsan beolvasni az Azure-t az Azure WAF az Azure-on belül vagy azon kívül futó webalkalmazások üzembe helyezéséhez. 

Az oktatóanyagban az Azure CLI használatával állíthatja be a WAF. Ugyanezt a dolgot a Azure Portal, a Azure PowerShell, a Azure Resource Manager vagy az Azure REST API-k használatával hajthatja végre. 

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:
> [!div class="checklist"]
> - Hozzon létre egy bejárati ajtót.
> - Hozzon létre egy Azure WAF szabályzatot.
> - Szabálykészlet konfigurálása WAF-házirendhez.
> - WAF-házirend hozzárendelése a bejárati ajtóhoz.
> - Egyéni tartomány konfigurálása.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Az oktatóanyagban szereplő utasítások az Azure CLI-t használják. [Tekintse meg ezt az útmutatót](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) az Azure CLI-vel való ismerkedéshez.

  > [!TIP] 
  > Az Azure CLI első lépéseinek egyszerű és gyors módja a [Bash Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).

- Győződjön meg arról, hogy a `front-door` bővítmény hozzá van adva az Azure CLI-hez:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Az oktatóanyagban használt parancsokkal kapcsolatos további információkért tekintse meg az [Azure CLI-referenciát az](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest)előtérben.

## <a name="create-an-azure-front-door-resource"></a>Azure-beli előtérben lévő erőforrás létrehozása

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: A védelemmel ellátni kívánt alkalmazás teljes tartományneve (FQDN). Például: `myapplication.contoso.com`.

`--accepted-protocols`: Azokat a protokollokat adja meg, amelyeknek az Azure bejáratát szeretné támogatni a webalkalmazás támogatásához. Például: `--accepted-protocols Http Https`.

`--name`: Az Azure-beli előtérben lévő erőforrás neve.

`--resource-group`: Az az erőforráscsoport, amelyben be szeretné helyezni az Azure bejárati erőforrását. További információ az erőforráscsoportok használatáról: [erőforráscsoportok kezelése az Azure-ban](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal).

A parancs futtatásakor kapott válaszban keresse meg a kulcsot `hostName` . Ezt az értéket egy későbbi lépésben kell megadnia. A a `hostName` létrehozott Azure-beli előtérben lévő erőforrás DNS-neve.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Azure-beli WAF-profil létrehozása az Azure-beli előtérben lévő erőforrásokkal való használathoz

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: Az új Azure WAF-szabályzat neve.

`--resource-group`: Az az erőforráscsoport, amelyben ezt a WAF-erőforrást be kívánja helyezni. 

Az előző CLI-kód létrehoz egy WAF házirendet, amely engedélyezve van, és ez megelőzési módban van. 

> [!NOTE] 
> Érdemes lehet az WAF szabályzatot észlelési módban létrehozni, és megfigyelni, hogyan észleli és naplózza a kártékony kérelmeket (anélkül, hogy blokkolja őket), mielőtt a védelmi mód használatára döntene.

A parancs futtatásakor kapott válaszban keresse meg a kulcsot `ID` . Ezt az értéket egy későbbi lépésben kell megadnia. 

A `ID` mezőnek a következő formátumúnak kell lennie:

/Subscriptions/**előfizetés azonosítója**/resourcegroups/**erőforráscsoport neve**/Providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF házirend neve**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Felügyelt szabálykészlet hozzáadása a WAF szabályzathoz

Felügyelt szabálykészlet WAF-házirendhez adható hozzá. A felügyelt szabálykészlet a Microsoft által készített és felügyelt szabályok halmaza, amely segít megvédeni a fenyegetések egy csoportjára. Ebben a példában két szabálykészlet hozzáadására van szükség:
- Az alapértelmezett szabálykészlet, amely segít a gyakori webes fenyegetések elleni védelemben. 
- A robot védelmi szabály beállítása, amely segít megvédeni a rosszindulatú robotok elleni védelmet.

Adja meg az alapértelmezett szabálykészlet:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

A robot Protection-szabálykészlet hozzáadása:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: Az Azure WAF-erőforráshoz megadott név.

`--resource-group`: Az az erőforráscsoport, amelyben a WAF-erőforrást helyezte.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>A WAF szabályzat hozzárendelése az Azure-beli bejárati erőforráshoz

Ebben a lépésben a webalkalmazás előtt létrehozott Azure bejárati erőforrással társítjuk a WAF szabályzatot:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: Az Azure-beli előtérben lévő erőforráshoz megadott név.

`--resource-group`: Az az erőforráscsoport, amelyben az Azure-beli bejárati erőforrást helyezte.

`--set`: Itt frissítheti az Azure-beli előtérben lévő `WebApplicationFirewallPolicyLink` `frontendEndpoint` erőforráshoz társított attribútumot az új WAF-házirenddel. Az oktatóanyag korábbi részében a WAF-profil létrehozásakor kapott válaszban a WAF szabályzat AZONOSÍTÓját kell használnia.

 > [!NOTE] 
> Az előző példa akkor alkalmazható, ha nem használ egyéni tartományt. Ha nem használ egyéni tartományokat a webalkalmazások eléréséhez, kihagyhatja a következő szakaszt. Ebben az esetben Önnek kell megadnia az ügyfeleit az Azure-beli `hostName` elülső ajtó erőforrásának létrehozásakor. Ezt használva a `hostName` webalkalmazáshoz juthatnak.

## <a name="configure-the-custom-domain-for-your-web-application"></a>A webalkalmazás egyéni tartományának konfigurálása

A webalkalmazás egyéni tartományneve az a felhasználó, aki az alkalmazásra hivatkozik. Például: www.contoso.com. Kezdetben ez az Egyéni tartománynév arra a helyre mutat, ahol a futott, mielőtt bevezette az Azure-előtérben. Miután hozzáadta az Azure-beli bejárati ajtót, és WAF az alkalmazást, az adott egyéni tartománynak megfelelő DNS-bejegyzésnek az Azure bejárati ajtó erőforrására kell mutatnia. Ezt a módosítást úgy hajthatja végre, hogy áthelyezi a DNS-kiszolgáló bejegyzését az Azure bejárati ajtajára, amelyet az Azure-beli előtérben `hostName` lévő erőforrás létrehozásakor észlelt.

A DNS-rekordok frissítésének konkrét lépései a DNS-szolgáltatótól függenek. Ha Azure DNSt használ a DNS-név üzemeltetéséhez, tekintse meg a [DNS-rekordok frissítésének](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) és az Azure-beli bejárati ajtóra mutató lépéseknek a dokumentációját `hostName` . 

Fontos megjegyezni, hogy ha az ügyfeleket a zóna csúcsának használatával kell megszereznie a webhelyhez (például contoso.com). Ebben az esetben az Azure DNS és az [alias bejegyzéstípust](https://docs.microsoft.com/azure/dns/dns-alias) kell használnia a DNS-név üzemeltetéséhez. 

Emellett frissítenie kell az Azure bejárati ajtajának konfigurációját, hogy [az egyéni tartományt hozzá lehessen adni](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) ahhoz, hogy tisztában legyen ezzel a leképezéssel.

Végül, ha egyéni tartományt használ a webalkalmazás eléréséhez, és engedélyezni szeretné a HTTPS protokollt, [be kell állítania az egyéni tartomány tanúsítványait az Azure-beli bejárati ajtón](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="lock-down-your-web-application"></a>A webalkalmazás zárolása

Javasoljuk, hogy csak az Azure-előtérben lévő élek kommunikáljanak a webalkalmazással. Így biztosíthatja, hogy senki ne kerüljék el az Azure-beli elülső ajtó védelmét, és közvetlenül hozzáférjen az alkalmazáshoz. A zárolás végrehajtásához tekintse [meg hogyan a háttérbeli hozzáférés zárolása csak az Azure bejárati ajtót?](https://docs.microsoft.com/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az oktatóanyagban használt erőforrásokra, az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítsa el az erőforráscsoportot, a bejárati ajtót és a WAF szabályzatot:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: Az oktatóanyagban használt összes erőforrás erőforráscsoport neve.

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan lehet elhárítani a bejárati ajtót, tekintse meg a hibaelhárítási útmutatókat:

> [!div class="nextstepaction"]
> [Gyakori útválasztási problémák elhárítása](front-door-troubleshoot-routing.md)

> [!div class="nextstepaction"]
> [Engedélyezett hitelesítésszolgáltatók](https://docs.microsoft.com/azure/frontdoor/front-door-troubleshoot-allowed-ca)
