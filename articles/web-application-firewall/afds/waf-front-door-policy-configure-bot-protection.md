---
title: Botvédelem konfigurálása az Azure Bejárati ajtóval (előzetes verzió) webalkalmazás-tűzfalhoz
description: Ismerje meg a webalkalmazás-tűzfalat (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934652"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Botvédelem konfigurálása a webalkalmazás tűzfala számára (előzetes verzió)
Ez a cikk bemutatja, hogyan konfigurálhatja a robot védelmi szabály az Azure Web Application Firewall (WAF) a bejárati ajtajához az Azure Portal használatával. A botvédelmi szabály cli, Azure PowerShell vagy Azure Resource Manager sablon használatával is konfigurálható.

> [!IMPORTANT]
> A robotvédelmi szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és előzetes szolgáltatásiszint-szerződéssel rendelkezik. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy alapvető WAF-szabályzatot a Bejárati ajtóhoz az [Azure-portál használatával a WAF-szabályzat létrehozása az Azure Bejárati ajtajához című](waf-front-door-create-portal.md)részben leírt utasításokat követve.

## <a name="enable-bot-protection-rule-set"></a>Botvédelmi szabálykészlet engedélyezése

A **Webalkalmazás** tűzfalházirend létrehozásakor a Felügyelt szabályok lapon először keresse meg a **Felügyelt szabálykészlet szakaszt,** jelölje be a legördülő menüben a **Microsoft_BotManager_1.0** szabály előtti jelölőnégyzetet, majd válassza a **Véleményezés + Létrehozás**lehetőséget.

   ![Bot védelmi szabály](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>További lépések

- További információ a [WAF figyelése.](waf-front-door-monitor.md)
