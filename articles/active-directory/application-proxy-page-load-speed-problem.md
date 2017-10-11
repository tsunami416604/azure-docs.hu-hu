---
title: "Az alkalmazásproxy alkalmazás betöltése túl sokáig tart |} Microsoft Docs"
description: "Elhárítása lap betöltési teljesítmény az Azure AD-alkalmazásproxy"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: ce462c90746e6af0dc201686557121665b82b93d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Az alkalmazásproxy alkalmazás betöltése túl sokáig tart

Ez a cikk segítenek megérteni, miért érdemes az Azure AD alkalmazásproxy alkalmazás betöltése sok időre lehet szükség, és mit tehet a probléma megoldásához.

## <a name="overview"></a>Áttekintés
Ha az alkalmazások dolgozik, de a hosszú várakozási ideje fogja látni, lehet néhány kisebb beállításokból állnak a hálózati topológia, érdemes lehet a sebesség növelése érdekében. Különböző topológiák értékelése, tekintse meg a [hálózati szempontok dokumentum](https://docs.microsoft.com/azure/active-directory/application-proxy-network-topology-considerations).

Ha ezeket a szempontokat nem segít, sajnos nem rendelkezik jelenleg tudunk teljesítményhangolás további javaslatokat. Az alkalmazásproxy-szolgáltatás, amely lehet, hogy közelebb vagy további adatközpontok bontja ki, mert megkezdése előfordulhat, hogy közvetlenül a továbbfejlesztett késés megjelenítéséhez. Azure-adatközpont teljes listájának megtekintéséhez lásd a [késés tesztoldalt](http://www.azurespeed.com/Azure/Latency). 

Az alkalmazásproxy-szolgáltatás a adatközpontjaiban található az a [összekötő portok vizsgálati eszköz](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Alkalmazásproxy data center helyeken visszajelzés 
Előfordulhat, hogy Azure-adatközpont, amely még nem jelennek meg a alkalmazásproxy, de az Ön a nagy késleltetésű fokozása vezetne. Az Adatközpont-helyen < aadapfeedback@microsoft.com > , tervezze meg, hogy bontsa ki a visszajelzést is használjuk.

Jelenleg néhány további képességeket, amelyek a bérlők számára, amelyet jelenleg a hosszú késések fordulnak elő a várakozási fokozásához dolgozik, és ügyeljen arra, hogy a megosztás után rendelkezésre álló dokumentáció.

## <a name="next-steps"></a>Következő lépések
[A meglévő helyszíni proxykiszolgálókkal működik](application-proxy-working-with-proxy-servers.md)
