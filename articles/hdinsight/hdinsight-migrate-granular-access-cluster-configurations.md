---
title: Konfiguráció-hozzáférés – Azure HDInsight-fürt módosításait
description: Ismerje meg a bizalmas fürt konfigurációs mezők érhetik el a módosításokat.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610214"
---
# <a name="changes-to-cluster-configuration-access"></a>Fürtkonfiguráció módosításai

Az Azure HDInsight SDK legújabb kiadása elérhetővé teszi a megváltozott néhány fontos további részletes szerepkör alapú hozzáférés támogatásához bizalmas információkhoz juthat. Mivel ezek része megváltozik, néhány **művelet lehet szükség** érintett módszer használata.

## <a name="sdk-for-net-500"></a>A .NET-hez 5.0.0 SDK

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek `ConfigurationOperationsExtensions.List` a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat. 
    - Csak HTTP-átjáró használt kérheti `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) most már elavult és felváltotta `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) és [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) elavultak. HTTP mindig engedélyezve van, így ezek a metódusok már nincs szükség.