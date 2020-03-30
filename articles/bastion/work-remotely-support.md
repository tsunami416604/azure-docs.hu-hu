---
title: 'A Bástya távhasználata: Azure-bástya'
description: Ez az oldal bemutatja, hogyan használhatja ki az Azure Bastion-t a covid-19-es világjárvány miatt távolról történő munkavégzés engedélyezéséhez.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: c0bba2560cccc208e2384421218ecebdfef65236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333073"
---
# <a name="working-remotely-using-azure-bastion"></a>Az Azure Bastion távoli használata

Az Azure Bastion kulcsfontosságú szerepet játszik a távoli munkahelyi forgatókönyvek támogatásában azáltal, hogy lehetővé teszi az internetkapcsolattal rendelkező felhasználók számára az Azure virtuális gépek elérését. Különösen lehetővé teszi a rendszergazdák számára, hogy az Azure-on futó alkalmazásaikat bármikor és a világ bármely pontjáról kezeljék.

>[!NOTE]
>Ez a cikk bemutatja, hogyan használhatja ki az Azure Bastion, az Azure, a Microsoft hálózat és az Azure-partneri ökoszisztéma segítségével a távoli munka és a covid-19-es válság miatt felmerülő hálózati problémák enyhítése.
>

## <a name="securely-access-virtual-machines"></a>A virtuális gépek biztonságos elérése

Pontosabban az Azure Bastion biztonságos és zökkenőmentes RDP/SSH kapcsolatot biztosít az Azure virtuális hálózaton belüli virtuális gépekhez, közvetlenül az Azure Portalon, nyilvános IP-cím használata nélkül. Az Azure Bastion architektúráról és a legfontosabb funkciókról a [Mi az Azure-bástya](bastion-overview.md)című.

Az Azure Bastion virtuális hálózatonként van telepítve, ami azt jelenti, hogy a vállalatok konfigurálhatnak és kezelhetnek egy Azure-bastiont, hogy gyorsan támogassák a távoli felhasználók hozzáférését a virtuális virtuális eszközökhöz egy Azure virtuális hálózaton belül. Az Azure Bastion létrehozásához és kezeléséhez a [Bástyagazdagép létrehozása](bastion-create-host-portal.md)című dokumentumban talál útmutatást.

## <a name="next-steps"></a>További lépések

* Konfigurálja az Azure Bastion az [Azure Portalon](bastion-create-host-portal.md), [a PowerShellen](bastion-create-host-powershell.md)vagy az Azure CLI-n keresztül.

* További információkért olvassa el a [Bástya GYIK-et.](bastion-faq.md)
