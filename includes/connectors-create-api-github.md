---
title: fájl belefoglalása
description: fájl belefoglalása
services: logic-apps
author: MandiOhlinger
ms.service: logic-apps
ms.topic: include
ms.date: 03/02/2018
ms.author: mandia
ms.custom: include file
ms.openlocfilehash: 11280e1678f52ede928cb2a85ea83add222e15fa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179382"
---
1. Az a [az Azure portal](https://portal.azure.com), hozzon létre egy üres logikai alkalmazást. 

2. A Logic Apps Designerben adja meg a "github" szűrőként. 

3. Válassza ki a GitHub-összekötő és az eseményindító, amelyet használni szeretne.

   ![A GitHub-összekötő és eseményindító kiválasztása](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Minden logikai alkalmazás munkafolyamat-trigger kell kezdődnie. Kiválaszthatja a műveleteket csak akkor, ha a logikai munkafolyamata egy eseményindítóval már indul. 

4. Válassza a korábban létrehozott egy kapcsolatot, **jelentkezzen be a** , megadhatja a Githubhoz használt hitelesítő adatait, amikor a rendszer kéri.  

   ![Jelentkezzen be GitHub hitelesítő adataival](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   A logikai alkalmazás csatlakoztatása és a GitHub-fiókhoz az adathozzáférés engedélyezéséhez ezeket a hitelesítő adatokat használ. 

5. Adja meg a GitHub-felhasználónevét és jelszavát, majd adja meg az engedélyt.

   ![Adja meg hitelesítő adatokat és az Engedélyezés megerősítése](./media/connectors-create-api-github/github-connector-authorize.png)   

   A kapcsolat ekkor létrejön az Azure Portalon, és készen áll a használatra.

6. Továbbra is a logikai alkalmazás munkafolyamatának meghatározása.

   ![További műveletek hozzáadása a logikai alkalmazás munkafolyamata](./media/connectors-create-api-github/github-connector-logic-app.png)

