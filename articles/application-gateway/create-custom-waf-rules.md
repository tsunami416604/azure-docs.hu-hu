---
title: Azure Web Application Firewall (WAF) v2 egyéni szabályok létrehozása és használata
description: Ez a cikk azt ismerteti, hogyan hozható létre a webalkalmazási tűzfal (WAF) v2 egyéni szabályai az Azure Application Gatewayban.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: dcfdec0a746406296616456f6e6b8c0eabddf4b5
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478587"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules"></a>Web Application Firewall v2 egyéni szabályok létrehozása és használata

Az Azure Application Gateway webalkalmazási tűzfal (WAF) v2 védelmet biztosít a webalkalmazások számára. Ezt a védelmet az Open Web Application Security Project (OWASP) alapszintű szabálykészlet (CRS) adja meg. Bizonyos esetekben előfordulhat, hogy saját egyéni szabályokat kell létrehoznia az adott igények kielégítése érdekében. További információ az egyéni WAF-szabályokról: [Egyéni webalkalmazási tűzfalszabályok – áttekintés](custom-waf-rules-overview.md).

Ez a cikk néhány példát mutat be a v2-WAF létrehozásához és használatához használható egyéni szabályokról. Ha szeretné megtudni, hogyan helyezhet üzembe egy WAF egyéni szabállyal Azure PowerShell használatával, tekintse meg a webalkalmazási [tűzfal egyéni szabályainak konfigurálása a Azure PowerShell használatával](configure-waf-custom-rules.md)című témakört.

>[!NOTE]
> Ha az Application Gateway nem a WAF szintet használja, akkor a jobb oldali ablaktáblán megjelenik az Application Gateway WAF szintre való frissítésének lehetősége.

![WAF engedélyezése][fig1]

## <a name="example-1"></a>1\. példa

Tudja, hogy van egy *evilbot* nevű robot, amelyet le szeretne tiltani a webhely bejárásával. Ebben az esetben letiltja a felhasználói ügynök *evilbot* a kérések fejlécében.

Logic: p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

És itt a megfelelő JSON:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

Az egyéni szabállyal telepített WAF megtekintéséhez lásd: webalkalmazási [tűzfal egyéni szabályának konfigurálása a Azure PowerShell használatával](configure-waf-custom-rules.md).

### <a name="example-1a"></a>1\. példa

Ugyanazt a dolgot egy reguláris kifejezés használatával is végrehajthatja:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

És a megfelelő JSON:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-2"></a>2\. példa

Az IP-címekről érkező összes kérést le szeretné tiltani a 198.168.5.4/24 tartományon belül.

Ebben a példában az IP-címtartomány összes forgalmát letiltja. A szabály neve *SajátSzabály1* , és a prioritás értéke 100.

Logic: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Itt látható a megfelelő JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.4/24"
            ]
          }
        ]
      }
    ]
  }
```

Megfelelő CRS-szabály:`SecRule REMOTE_ADDR "@ipMatch 192.168.5.4/24" "id:7001,deny"`

## <a name="example-3"></a>3\. példa

Ebben a példában le szeretné tiltani a felhasználói ügynök *evilbot*, valamint a 192.168.5.4/24 tartomány forgalmát. Ennek elvégzéséhez két különálló egyeztetési feltételt hozhat létre, és mindkettőt ugyanabban a szabályban helyezheti el. Ez biztosítja, hogy a *evilbot* a felhasználói ügynök fejlécében **és** a 192.168.5.4/24 tartományból származó IP-címek is le legyenek tiltva.

Logic: p **és** q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

Itt látható a megfelelő JSON:

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 100, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": true, 
              "matchValues": [ 
                "192.168.5.4/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-4"></a>4\. példa

Ebben a példában le szeretné tiltani, hogy a kérelem vagy a *192.168.5.4/24*IP-címtartomány kívül esik-e, vagy a felhasználói ügynök karakterlánca nem a *Chrome* (azaz a felhasználó nem használja a Chrome böngészőt). Mivel ez a logika **vagy**a-t használja, a két feltétel külön szabályokban szerepel, ahogy az alábbi példában is látható. a *SajátSzabály1* és a *SajátSzabály2* egyaránt meg kell egyezniük a forgalom blokkolásával.

Logika: **nem** (p **és** q) = **nem** p **vagy nem** q.

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 200 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

És a megfelelő JSON:

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.4/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 200,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-5"></a>5\. példa

Le szeretné tiltani az egyéni SQLI. Mivel az itt használt logika **vagy**, és az összes érték a *RequestUri*található, az összes *MatchValues* vesszővel tagolt listában lehet.

Logic: p **vagy** q **vagy** r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Megfelelő JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 100
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

Alternatív Azure PowerShell:

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 200 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 300 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

Megfelelő JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="next-steps"></a>További lépések

Az egyéni szabályok létrehozása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információ: [Application Gateway diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
