---
title: "Azure Security Center riasztásait integrálása az Azure naplóelemzés integrációs |} Microsoft Docs"
description: "Ez a cikk segít a Security Center riasztásait integrálása az Azure naplóelemzés integráció az első lépései."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: 229f83d4d74837e506c30a0fd8c704da2b7dc12a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration"></a>Azure Security Center riasztásait integrálása az Azure naplóelemzés integráció
Számos biztonsági műveletek és incidensekre adott reakciók csapatok támaszkodnak biztonsági adatai és az esemény felügyeleti SIEM-megoldás triaging és biztonsági riasztások kivizsgálásának kiindulási pontként. Az Azure napló integráció integrálható Azure Security Center riasztásait az SIEM-megoldás.

Azure naplóelemzés integrációs jelenleg a HP ArcSight Splunk és az IBM QRadar támogatja.

## <a name="what-logs-can-i-integrate"></a>Milyen naplókat is integrálhatja?
Azure kiterjedt naplózás minden szolgáltatás eredményez. Ezek a naplók kategóriába sorolni:

* **Ellenőrzés/Management-naplók** , amelyek biztosítják, hogy az Azure Resource Manager LÉTREHOZNI, UPDATE és DELETE műveletek láthatósága. A vezérlő vezérlősík események illesztett az Azure tevékenység található
* **Az adatnaplók Vezérlősík** , amelyek biztosítják, hogy az egy Azure-erőforrás használatakor kiváltott események láthatósága. Példa: a Windows eseménynaplóban, ahol letölthető biztonsági események adatait az Eseménynapló használatával biztonsági csatorna. Adatok vezérlősík események (amely a virtuális gép vagy az Azure-szolgáltatások által előállított) által az Azure diagnosztikai naplók illesztett.

Azure naplóelemzés integrációs jelenleg integrálása támogatja:

* Az Azure virtuális gép naplók
* Az Azure felügyeleti naplók
* Azure Security Center riasztásait

## <a name="install-azure-log-integration"></a>Azure naplóelemzés integráció telepítése
Töltse le [Azure naplóelemzés integrációs](https://www.microsoft.com/download/details.aspx?id=53324).

Az Azure naplóelemzés integrációs szolgáltatás telemetriai adatokat gyűjt a számítógépről, amelyen telepítve van.  Az összegyűjtött telemetrikus adatok van:

* Az Azure naplóelemzés integráció során bekövetkező kivételek
* A lekérdezések és a feldolgozott események száma kapcsolatos metrikákat
* Mely Azlog.exe kapcsolatos parancssori kapcsolók használt statisztikák

> [!NOTE]
> Ez a beállítás jelének kapcsolhatja ki telemetrikus adatok gyűjtését.
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Az Azure naplói és a Security Center riasztások integrálása
1. Nyissa meg a parancssort és **cd** történő **c:\Program Files\Microsoft Azure napló integrációs**.
2. Futtassa a **azlog createazureid** parancs futtatásával hozzon létre egy [Azure Active Directory szolgáltatás egyszerű](../active-directory/active-directory-application-objects.md) az az Azure Active Directory (AD) bérlők, amely az Azure-előfizetések tárolni.

    Az Azure bejelentkezési kéri.

   > [!NOTE]
   > Az előfizetés az előfizetés tulajdonosa vagy társadminisztrátorának kell lennie.
   >
   >

    Hitelesítés az Azure-bA az Azure AD segítségével történik.  Az Azure naplóelemzés integráció egyszerű szolgáltatás létrehozása hoz létre az Azure AD identity arra, hogy az Azure-előfizetések olvasási hozzáférést.
3. Futtassa a **azlog engedélyezik <SubscriptionID>**  parancs az előfizetés olvasási hozzáférés hozzárendelése a szolgáltatás egyszerű, a 2. Ha nem adja meg a **SubscriptionID**, akkor a szolgáltatás egyszerű szerepét az olvasó rendelkezik hozzáféréssel előfizetéseken.

   > [!NOTE]
   > Figyelmeztetések jelenhet meg, ha futtatja a **engedélyezik** parancs után azonnal a **createazureid** parancsot. Néhány történő létrehozásakor a Azure AD-fiókot, és ha a fiók használható közötti késés van. Ha futtatása után nagyjából 10 másodpercet vár a **createazureid** parancsot kell futtatni a **engedélyezik** parancsot, majd a figyelmeztetések nem szabadna megjelennie.
   >
   >
4. Ellenőrizze, győződjön meg arról, hogy a naplófájlok JSON vannak-e a következő mappák:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Ellenőrizze a következő mappák győződjön meg arról, hogy létezik-e a Security Center riasztásait a bennük foglalt:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. A megfelelő mappát a SIEM fájl továbbító összekötőt konfigurálja. Az eljárás használ SIEM függően változik.

## <a name="next-steps"></a>További lépések
Azure tevékenységi naplóit és erőforrástulajdonság-meghatározások kapcsolatos további információkért lásd:

* [Auditálási műveletek a Resource Managerben](../azure-resource-manager/resource-group-audit.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági híreket és információkat.
