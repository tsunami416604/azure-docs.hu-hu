---
title: Az Azure AD Connect telepítése az SQL delegált rendszergazdai engedélyeivel | Microsoft dokumentumok
description: Ez a témakör az Azure AD Connect egy frissítését ismerteti, amely lehetővé teszi a telepítést egy olyan fiók használatával, amely csak SQL dbo-engedélyekkel rendelkezik.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60243549"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Az Azure AD Connect telepítése SQL-lel delegált rendszergazdai engedélyekkel
Az Azure AD Connect legutóbbi létrehozása előtt a felügyeleti delegálás nem támogatott az SQL-t igénylő konfigurációk telepítésekor.  Azoknak a felhasználóknak, akik telepíteni kívánták az Azure AD Connectet, kiszolgálói rendszergazdai (SA) engedélyekkel kellett rendelkezniük az SQL-kiszolgálón.

Az Azure AD Connect legújabb kiadásával az adatbázis kiépítését az SQL-rendszergazda már sávon kívül is elvégezheti, majd az Azure AD Connect-rendszergazda telepítheti az adatbázis tulajdonosi jogaival.

## <a name="before-you-begin"></a>Előkészületek
A szolgáltatás használatához tisztában kell lennie azzal, hogy több mozgó alkatrész van, és mindegyik ben egy másik rendszergazda is részt vehet a szervezetben.  Az alábbi táblázat összefoglalja az egyes szerepköröket és az Azure AD Connect ezzel a funkcióval történő üzembe helyezésére vonatkozó feladataikat.

|Szerepkör|Leírás|
|-----|-----|
|Tartomány- vagy Erdő AD-rendszergazda|Létrehozza a tartományszintű szolgáltatásfiókot, amelyet az Azure AD Connect a szinkronizálási szolgáltatás futtatásához használ.  A szolgáltatásfiókokról a [Fiókok és engedélyek](reference-connect-accounts-permissions.md)című témakörben talál további információt.
|SQL-rendszergazda|Létrehozza az ADSync-adatbázist, és bejelentkezési + dbo hozzáférést biztosít az Azure AD Connect rendszergazdájához és a tartomány/erdő rendszergazdája által létrehozott szolgáltatásfiókhoz.|
Az Azure AD Connect rendszergazdája|Telepíti az Azure AD Connectet, és megadja a szolgáltatásfiókot az egyéni telepítés során.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Az Azure AD Connect SQL delegált engedélyek használatával történő telepítésének lépései
Az adatbázis sávon kívüli kiépítéséhez és az Azure AD Connect adatbázis-tulajdonosi engedélyekkel történő telepítéséhez kövesse az alábbi lépéseket.

>[!NOTE]
>Bár ez nem szükséges, **erősen ajánlott,** hogy a Latin1_General_CI_AS rendezés e kiválasztáskor az adatbázis létrehozásakor.


1. Az SQL-rendszergazda hozza létre az ADSync adatbázist a kis- és nagybetűk megkülönböztetése **(Latin1_General_CI_AS)** beállítással.  Az adatbázis neve **ADSync**.  A helyreállítási modell, a kompatibilitási szint és a elszigeteléstípusa az Azure AD Connect telepítésekor a megfelelő értékekre frissül.  Azonban a rendezési sorrendet kell beállítani helyesen az SQL-rendszergazda, különben az Azure AD Connect blokkolja a telepítést.  A biztonsági létrehozás helyreállításához törölnie kell, majd újra létre kell hoznia az adatbázist.
 
   ![Rendezés](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Adja meg az Azure AD Connect rendszergazdájának és a tartományi szolgáltatásfióknak a következő engedélyeket:
   - SQL-bejelentkezés 
   - **adatbázis tulajdonosának(dbo)** jogait.
 
   ![Engedélyek](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Az Azure AD Connect nem támogatja a beágyazott tagsággal rendelkező bejelentkezéseket.  Ez azt jelenti, hogy az Azure AD Connect rendszergazdai fiókját és a tartományi szolgáltatásfiókot egy dbo-jogosultságokkal ellátott bejelentkezéshez kell kapcsolni.  Nem lehet egyszerűen egy olyan csoport tagja, amely dbo jogokkal rendelkező bejelentkezéshez van rendelve.

3. Küldjön egy e-mailt az Azure AD Connect rendszergazdájának, amely jelzi az SQL-kiszolgáló és a példány nevét, amelyet az Azure AD Connect telepítésekor használni kell.

## <a name="additional-information"></a>További információ
Az adatbázis kiépítése után az Azure AD Connect rendszergazdája saját kényelmük nek megfelelően telepítheti és konfigurálhatja a helyszíni szinkronizálást.

Abban az esetben, ha az SQL-rendszergazda visszaállította az ADSync-adatbázist egy korábbi Azure AD Connect biztonsági mentésből, telepítenie kell az új Azure AD Connect-kiszolgálót egy meglévő adatbázis használatával. Az Azure AD Connect meglévő adatbázissal történő telepítéséről az [Azure AD Connect telepítése meglévő ADSync-adatbázis használatával című](how-to-connect-install-existing-database.md)témakörben talál további információt.

## <a name="next-steps"></a>További lépések
- [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](how-to-connect-install-express.md)
- [Az Azure AD Connect testreszabott telepítése](how-to-connect-install-custom.md)
- [Az Azure AD Connect telepítése meglévő ADSync-adatbázis használatával](how-to-connect-install-existing-database.md)  
