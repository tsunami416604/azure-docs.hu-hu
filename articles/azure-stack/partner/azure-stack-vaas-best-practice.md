---
title: Az Azure Stack érvényesítési ajánlott eljárások. | Microsoft Docs
description: Ez a cikk gyakorlati tanácsok érvényesítési szolgáltatásként is rendelkezik.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: John.Haskin
ms.openlocfilehash: 988b32d378b9affccb79f3351761f0eca5c91346
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651467"
---
# <a name="best-practices-for-validation-as-a-service"></a>Ajánlott eljárások a szolgáltatás érvényesítése

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk ismerteti az ajánlott eljárások az érvényesítési (VaaS) szolgáltatás-erőforrások kezelése. VaaS erőforrások áttekintéséhez lásd: [mint szolgáltatásra kulcs érvényesítése](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Megoldások kezelése

### <a name="naming-convention-for-vaas-solutions"></a>Elnevezési VaaS megoldások

Egy egységes kulcselnevezési konvenció használata VaaS regisztrált összes megoldás. Ha például a megoldás nevét is összetevődni az alábbi tulajdonságait:

|Terméknév | 1 elem egyedi hardver | Egyedi hardver elem 2 | Megoldás neve
|---|---|---|---|
A megoldás XYZ |  Az összes Flash | A kapcsoló X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Mikor hozzon létre egy új VaaS megoldás

Az azonos VaaS megoldást használni, ha ugyanazt a hardvert Termékváltozat munkafolyamatokat futtat. Csak akkor, ha a hardver SKU módosítják egy új VaaS megoldást kell létrehozni.

## <a name="workflow-management"></a>Munkafolyamatok kezelése

### <a name="naming-convention-for-vaas-workflows"></a>A munkafolyamatok VaaS elnevezési konvenciót

Egy egységes kulcselnevezési konvenció használata minden VaaS munkafolyamat-Futtatás. Például a szerkezetet munkafolyamat neve a build tulajdonságait az alábbi módon:

|Buildszám (nagy) | Dátum | Megoldás mérete | A munkafolyamat neve
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>További lépések

- Ismerje meg [mint szolgáltatásra kulcs érvényesítése](azure-stack-vaas-key-concepts.md)