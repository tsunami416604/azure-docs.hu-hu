---
title: Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: A bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/28/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e7b0c379783af2f9131d487f45c0f4e2009e258
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232151"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon

The reporting architecture in Azure Active Directory (Azure AD) consists of the following components:

- **Tevékenység** 
    - **Sign-ins** – Information about the usage of managed applications and user sign-in activities.
    - **Audit logs** - [Audit logs](concept-audit-logs.md) provide system activity information about users and group management, managed applications, and directory activities.
- **Biztonság** 
    - **Risky sign-ins** - A [risky sign-in](concept-risky-sign-ins.md) is an indicator for a sign-in attempt by someone who isn't the legitimate owner of a user account.
    - **Users flagged for risk** - A [risky user](concept-user-at-risk.md) is an indicator for a user account that might have been compromised.

This article gives you an overview of the sign-ins report.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

* Users in the Security Administrator, Security Reader, Global Reader, and Report Reader roles
* Global Administrators
* Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?

* Your tenant must have an Azure AD Premium license associated with it to see the all up sign-in activity report. See [Getting started with Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) to upgrade your Azure Active Directory edition. It will take a couple of days for the data to show up in the reports after you upgrade to a premium license with no data activities before the upgrade.

## <a name="sign-ins-report"></a>Sign-ins report

The user sign-ins report provides answers to the following questions:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

Start with [Azure portal](https://portal.azure.com). To access the sign-ins report select **Sign-ins**, continue to the **Monitoring.** It may take up to two hours for some sign-in records to show up in the portal.

![Sign-in activity](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Bejelentkezési tevékenység")

> [!IMPORTANT]
> The sign-ins report only displays the **interactive** sign-ins, that is, sign-ins where a user manually signs in using their username and password. Non-interactive sign-ins, such as service-to-service authentication, are not displayed in the sign-ins report. 

A bejelentkezési napló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- A bejelentkezés dátuma
- A kapcsolódó felhasználó
- The application the user has signed in to
- A bejelentkezési állapot
- A kockázatészlelés állapota
- A többtényezős hitelesítési (MFA-) követelmény állapota

![Sign-in activity](./media/concept-sign-ins/sign-in-activity.png "Bejelentkezési tevékenység")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Sign-in activity](./media/concept-sign-ins/19.png "Bejelentkezési tevékenység")

Displays additional fields or remove fields that are already displayed.

![Sign-in activity](./media/concept-sign-ins/02.png "Bejelentkezési tevékenység")

Select an item in the list view to get more detailed information.

![Sign-in activity](./media/concept-sign-ins/basic-sign-in.png "Bejelentkezési tevékenység")

> [!NOTE]
> Customers can now troubleshoot Conditional Access policies through all sign-in reports. By clicking on the **Conditional Access** tab for a sign-in record, customers can review the Conditional Access status and dive into the details of the policies that applied to the sign-in and the result for each policy.
> For more information, see the [Frequently asked questions about CA information in all sign-ins](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

First, narrowing down the reported data to a level that works for you. Second, filter sign-ins data using date field as default filter. Azure AD provides you with a broad range of additional filters you can set.

![Sign-in activity](./media/concept-sign-ins/04.png "Bejelentkezési tevékenység")

A **Felhasználó** szűrővel egy konkrét felhasználó nevét vagy egyszerű felhasználónevét (UPN) adhatja meg.

Az **Alkalmazás** szűrővel egy konkrét alkalmazás nevét adhatja meg.

A **Bejelentkezési állapot** szűrővel az alábbi lehetőségek közül választhat:

- Mind
- Sikeres
- Hiba

The **Conditional Access** filter enables you to select the CA policy status for the sign-in:

- Mind
- Not Applied
- Sikeres
- Hiba

A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- One month
- 7 nap
- 24 óra
- Egyéni időintervallum

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

Ha további mezőket ad hozzá a bejelentkezési nézethez, a rendszer automatikusan hozzáadja a mezőket a szűrőlistához. Például az **Ügyfélalkalmazás** mező listához való hozzáadásával kap egy további szűrőlehetőséget, amellyel a következő szűrőket állíthatja be:  
![Sign-in activity](./media/concept-sign-ins/12.png "Bejelentkezési tevékenység")

- **Browser**  
    This filter shows all events where sign-in attempts were attempted using browser flows.
- **Exchange ActiveSync (supported)**  
    This filter shows all sign-in attempts where the Exchange ActiveSync (EAS) protocol has been attempted from supported platforms like iOS, Android, and Windows Phone.
- **Exchange ActiveSync (unsupported)**  
    This filter shows all sign-in attempts where the EAS protocol has been attempted from unsupported platforms like, Linux distros.
- **Mobile Apps and Desktop clients** The filter shows all sign-in attempts that were not using browser flows. For example, mobile apps from any platform using any protocol or from Desktop client apps like Office on Windows or MacOS.
  
- **Other clients**
    - **IMAP**  
        A legacy mail client using IMAP to retrieve email.
    - **MAPI**  
        Office 2013, where ADAL is enabled and it is using MAPI.
    - **Old Office clients**  
        Office 2013 in its default configuration where ADAL is not enabled and it is using MAPI, or Office 2016 where ADAL has been disabled.
    - **POP**  
        A legacy mail client using POP3 to retrieve email.
    - **SMTP**  
        A legacy mail client using SMTP to send email.

## <a name="download-sign-in-activities"></a>Bejelentkezési tevékenységek letöltése

Click the **Download** option to create a CSV or JSON file of the most recent 250,000 records. Start with [download the sign-ins data](quickstart-download-sign-in-report.md) if you want to work with it outside the Azure portal.  

![Letöltés](./media/concept-sign-ins/71.png "Letöltés")

> [!IMPORTANT]
> The number of records you can download is constrained by the [Azure Active Directory report retention policies](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Sign-ins data shortcuts

Azure AD and the Azure portal both provide you with additional entry points to sign-ins data:

- The Identity security protection overview
- Felhasználók
- Csoportok
- Vállalati alkalmazások

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Users sign-ins data in Identity security protection

The user sign-in graph in the **Identity security protection** overview page shows weekly aggregations of sign-ins. The default for the time period is 30 days.

![Sign-in activity](./media/concept-sign-ins/06.png "Bejelentkezési tevékenység")

A bejelentkezési grafikon egyik napjára kattintva áttekintést kap az adott nap bejelentkezési tevékenységeiről.

A bejelentkezési tevékenységek listájának minden sora a következőkről ad információkat:

* Ki jelentkezett be?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezés állapota?
* Mi a bejelentkezés MFA-állapota?

Az elemekre kattintva részletes információk érhetők el a bejelentkezési műveletről:

- Felhasználóazonosító
- Felhasználó
- Felhasználónév
- Alkalmazásazonosító
- Jelentkezés
- Ügyfél
- Földrajzi egység
- IP-cím
- Dátum
- Az MFA megadása kötelező
- Bejelentkezési állapot

> [!NOTE]
> IP addresses are issued in such a way that there is no definitive connection between an IP address and where the computer with that address is physically located. Mapping IP addresses is complicated by the fact that mobile providers and VPNs issue IP addresses from central pools that are often very far from where the client device is actually used. Currently in Azure AD reports, converting IP address to a physical location is a best effort based on traces, registry data, reverse look ups and other information.

A **Felhasználók** oldalon teljes körű áttekintést kaphat az összes felhasználói bejelentkezésről a **Tevékenységek** szakaszban található **Bejelentkezések** elemre kattintva.

![Sign-in activity](./media/concept-sign-ins/08.png "Bejelentkezési tevékenység")

## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* What are the top three applications in your organization?
* How is my newest application doing?

The entry point to this data is the top three applications in your organization. The data is contained within the last 30 days report in the **Overview** section under **Enterprise applications**.

![Sign-in activity](./media/concept-sign-ins/10.png "Bejelentkezési tevékenység")

The app-usage graphs weekly aggregations of sign-ins for your top three applications in a given time period. Az alapértelmezett időszak 30 nap.

![Sign-in activity](./media/concept-sign-ins/graph-chart.png "Bejelentkezési tevékenység")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentéskészítés](./media/concept-sign-ins/single-app-usage-graph.png "Jelentések")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

## <a name="office-365-activity-logs"></a>Office 365 activity logs

You can view Office 365 activity logs from the [Microsoft 365 admin center](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Consider the point  that, Office 365 activity and Azure AD activity logs share a significant number of the directory resources. Only the Microsoft 365 admin center provides a full view of the Office 365 activity logs. 

You can also access the Office 365 activity logs programmatically by using the [Office 365 Management APIs](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Következő lépések

* [Sign-in activity report error codes](reference-sign-ins-error-codes.md)
* [Azure AD data retention policies](reference-reports-data-retention.md)
* [Azure AD report latencies](reference-reports-latencies.md)

