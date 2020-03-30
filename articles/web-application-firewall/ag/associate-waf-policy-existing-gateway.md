---
title: Webalkalmazás-tűzfal házirend társítása meglévő Azure-alkalmazásátjáróhoz
description: Megtudhatja, hogyan társíthat webalkalmazás-tűzfal-szabályzatot egy meglévő Azure Application Gateway-hez.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083905"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>WAF-házirend társítása meglévő alkalmazásátjáróhoz

Az Azure PowerShell segítségével [waf-szabályzatot hozhat létre,](create-waf-policy-ag.md)de lehet, hogy már rendelkezik egy Application Gateway-rel, és csak egy WAF-szabályzatot szeretne hozzárendelni. Ebben a cikkben, akkor nem csak, hogy; waf-szabályzatot hoz létre, és társítja azt egy már meglévő Application Gateway-hez. 

1. Az alkalmazásátjáró ra és a tűzfalházirendre vonatkozó házirend beszerezhető. Ha nem rendelkezik meglévő tűzfalházirenddel, olvassa el a 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Nem kötelező) Hozzon létre egy tűzfalházirendet.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Ha ezt a WAF-házirendet a WAF-konfigurációról a WAF-házirendre való áttéréshez hozza létre, akkor a házirendnek a régi Konfigurációpontos másolatának kell lennie. Ez azt jelenti, hogy minden kizárásnak, egyéni szabálynak, letiltott szabálycsoportnak stb.-nak pontosan ugyanolyannak kell lennie, mint a WAF konfigurációban.
3. (Nem kötelező) A WAF-szabályzatot igény szerint konfigurálhatja. Ez magában foglalja az egyéni szabályokat, a szabályok/szabálycsoportok letiltását, a kizárásokat, a fájlfeltöltési korlátok beállítását stb. Ha kihagyja ezt a lépést, az összes alapértelmezett beállítás ki lesz jelölve. 
   
4. Mentse a házirendet, és csatolja az Application Gateway-hez. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>További lépések
[További információ az egyéni szabályokról.](configure-waf-custom-rules.md)
