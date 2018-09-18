---
title: Nyilvános IP-címek hozzáadása az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan több nyilvános IP-címek hozzáadása az Azure Stackhez.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: scottnap
ms.openlocfilehash: b401139d417674cf58d2db264b442d7588cc34ba
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986230"
---
# <a name="add-public-ip-addresses"></a>Nyilvános IP-címek hozzáadása
*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*  

Ismerje meg, hogyan több nyilvános IP-címek hozzáadása az Azure Stackhez.  Ebben a cikkben a címeket, amelyek külső, nyilvános IP-címek nevezzük, de az Azure Stack az jelenti, tekintse meg az IP-címblokkok hozzáadása a külső hálózathoz.  E külső hálózathoz tartozó nyilvános internetre irányítható vagy az intraneten és használja privát címterét nem számít, hogy ez a cikk az alkalmazásában.  A lépések ugyanazok. 

## <a name="add-a-public-ip-address-pool"></a>Nyilvános IP-címkészlet hozzáadása
Nyilvános IP-címek az Azure Stack-rendszer a kezdeti telepítés után bármikor hozzáadhat az Azure Stack rendszer. Tekintse meg, hogy miként [nézet nyilvános IP-címek használatának](azure-stack-viewing-public-ip-address-consumption.md) , milyen a jelenlegi felhasználás és nyilvános IP-cím rendelkezésre állási van az Azure Stack.

Az Azure Stack egy új nyilvános IP-cím blokkolása hozzáadásának magas szinten, a következőhöz hasonló:

 ![Adja hozzá az IP-folyamat](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>A címterület lekérését a szolgáltató
Az első lépésben kell tennie, hogy a-Címblokk hozzáadása az Azure Stackhez szeretne beszerezni.  Attól függően, hol szerezze be a címet block (wasabi) szüksége, érdemes figyelembe venni, mi a átfutási ideje és kezelni ezt a beállítást a sebesség, amellyel az Azure Stackben nyilvános IP-címeket használ fel ellen.  

> [!IMPORTANT]
> Az Azure Stack bármely Ön által megadott, mindaddig, amíg érvényes címterület, és nem fedi át az Azure Stackben meglévő címtartománnyal címterület fogja fogadni.  Ellenőrizze, hogy használja-e. érvényes címterület irányítható és egymást nem átfedő a külső hálózattal, amelyhez az Azure Stack csatlakoztatva van.  Miután hozzáadta a tartományt az Azure Stackhez, nem távolítható el.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Az IP-címtartomány hozzáadása az Azure Stackhez

1. Egy webböngészőben nyissa meg a felügyeleti portál irányítópultján.  Ebben a példában használjuk https://adminportal.local.azurestack.external.  
2.  Jelentkezzen be az Azure Stack felügyeleti portálon, a felhő üzemeltetője.
3.  Az alapértelmezett irányítópult – keresse meg a régió felügyeleti listáját, és válassza ki a kezelni kívánt, ebben a példában helyi régiót.
4.  Az erőforrás-szolgáltatók csempére, majd kattintson a hálózati erőforrás-szolgáltató található.
5.  Kattintson a nyilvános IP-készletek használata csempe.
6.  Kattintson a készlet IP hozzáadása gombra.
7.  Adja meg az IP-készlet nevét.  A csak, lehetővé teszi, hogy könnyen azonosíthassa az IP-készlet, így meg lehet hívni bármire átnevezhető választott név.  Bevált gyakorlat, hogy a neve megegyezik a címtartományt, de nem szükséges.
8.   Adja meg a-Címblokk szeretne hozzáadni a CIDR-jelölésrendszerben.  Például: 192.168.203.0/24
9.  Ha megad egy érvényes CIDR-tartományt a cím címtartomány (CIDR-blokk) mezőbe a kezdő IP-cím, záró IP-cím és a rendelkezésre álló IP-címek mezők automatikusan kitöltődnek.  Ezek csak olvasható, és automatikusan jönnek létre, így ezek a cím tartományt mező értékének módosítása nélkül nem módosítható.
10. Minden állapíthatja meg a panelen található információk áttekintése után kijavításához kattintson az Ok gombra a módosítás véglegesítéséhez és a címtartomány hozzáadása az Azure Stackhez.

## <a name="update-the-acls-on-your-top-of-rack-switches"></a>Hozzáférés-vezérlési listák a tor kapcsolók frissítése
A legutolsó dolog, és engedélyezze az újonnan hozzáadott IP-címtartomány működéséhez szükséges, hogy a hozzáférés-vezérlési listák (ACL) a Top-of-Rack (ToR) kapcsoló a frissítése.  Hozzáférés-vezérlési listák a ToR-kapcsolók oly módon, hogy csatlakozik az Azure Stack-en kívül az újonnan hozzáadott IP-címtartomány nem fog működni az új tartományon amíg zárolva hozzáadódik a hozzáférés-vezérlési listák a kapcsolón.  

A készülék gyártójától és vele a hozzáférés-vezérlési listák a ToR-kapcsolók frissíteni kell.  Az eszközök támogatott módon ehhez szükség van.


## <a name="next-steps"></a>További lépések 
[Felülvizsgálat skálázási egység csomópont műveletek](azure-stack-node-actions.md) 
