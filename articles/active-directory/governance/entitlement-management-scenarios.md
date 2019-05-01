---
title: Az Azure AD tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory alkalmazási helyzetek
description: Ismerje meg, a magas szintű lépéseket kell követni a gyakori forgatókönyvek az Azure Active Directory tagjogosultság-kezelés (előzetes verzió).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96442a9d49581da6841fa7acb8329354ec727f60
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918479"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Gyakori forgatókönyvek az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Többféleképpen is konfigurálható tagjogosultság-kezelés a szervezet számára. Azonban Ön bevezető, hogy annak megértése, a gyakori forgatókönyvek rendszergazdák, a jóváhagyók és a kérelmezőktől a hozzáférést.

## <a name="administrators"></a>Rendszergazdák

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Tagjogosultság-kezelés az új vagyok, és szeretném segítséget az első lépések

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | [Az oktatóanyag az első hozzáférési csomag létrehozása](entitlement-management-access-package-first.md) | [![Az Azure portal ikon](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Szeretném, hogy a felhasználók saját címtárbeli kérelmezzenek hozzáférést a csoportok, alkalmazások vagy SharePoint-webhelyek

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Hozzon létre egy új hozzáférési csomagot a katalógusban található](entitlement-management-access-package-create.md#start-new-access-package) | ![Hozzáférés csomag létrehozása](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Csomag el az erőforrás-szerepkörök hozzáadása](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Csoportok</li><li>Alkalmazások</li><li>SharePoint-webhelyek</li></ul> | ![Erőforrás-szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Szabályzat hozzáadása](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>A címtárban lévő felhasználók számára</li><li>Jóváhagyás szükséges</li><li>Elévülési beállítások</li></ul> | ![Házirend hozzáadása](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Szeretném, hogy a felhasználók a saját üzleti partnerek (beleértve a felhasználók még nincs a címtáramban) hozzáférés kéréséhez csoportok, alkalmazások vagy SharePoint-webhelyek

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Hozzon létre egy új hozzáférési csomagot a katalógusban található](entitlement-management-access-package-create.md#start-new-access-package) | ![Hozzáférés csomag létrehozása](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Csomag el az erőforrás-szerepkörök hozzáadása](entitlement-management-access-package-edit.md#add-resource-roles) | ![Erőforrás-szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Külső felhasználók számára szabályzat hozzáadása](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Nem a címtárban található felhasználók számára</li><li>Jóváhagyás szükséges</li><li>Elévülési beállítások</li></ul> | ![Külső felhasználók számára a szabályzat hozzáadása](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Küldje el a saját hozzáférési portál hivatkozást lekérni a hozzáférés az üzleti partner-csomag](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Üzleti partner hivatkozást megoszthatja a felhasználók</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>A csoportok, alkalmazások vagy SharePoint-webhelyek egy hozzáférés-csomag módosítása

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** Nyissa meg a hozzáférés-csomagot | ![Erőforrás-szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Adja hozzá, vagy távolítsa el az erőforrás-szerepkörökkel](entitlement-management-access-package-edit.md#add-resource-roles) | ![Erőforrás-szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Kinek van, a hozzárendelés csoportokhoz, alkalmazások vagy SharePoint-webhelyek megtekinteni kívánt

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** Nyisson meg egy hozzáférés-csomag | ![Erőforrás-szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Hozzárendelések megtekintése](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Mely felhasználók férhetnek hozzá egy hozzáférési csomag nézet</li><li>Melyik felhasználó hozzáférése lejárt megtekintése</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Szeretném megtekinteni a csoportok, alkalmazások vagy SharePoint-webhelyek egy felhasználónak hozzáférése van

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | [Hozzárendelések – felhasználói jelentés megtekintése](entitlement-management-reports.md)<ul><li>Nézet, ha a kért és jóváhagyott ki</li></ul> |  |

## <a name="approvers"></a>Jóváhagyók

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Szeretném csoportok, alkalmazások vagy SharePoint-helyek eléréséhez kérések jóváhagyása

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [A saját hozzáférési portál megnyitott kérelem](entitlement-management-request-approve.md#open-request) | [![A hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Hozzáférési kérelem jóváhagyása](entitlement-management-request-approve.md#approve-or-deny-request) | ![Hozzáférés jóváhagyása](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Kérelmezőktől a hozzáférést

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Szeretnék megtekinteni a csoportok, alkalmazások vagy SharePoint-webhelyek számomra is elérhető, és hozzáférés kérése

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Jelentkezzen be a saját Access Portalra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![A hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Keresse meg a hozzáférés-csomag |  |
> | **3.** [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package) | ![Hozzáférés kérése](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Külső felhasználó vagyok, és szeretném csoportok, alkalmazások vagy SharePoint-webhelyek közvetlen hivatkozásával hozzáférés kérése

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [A kapott saját hozzáférési portál hivatkozás keresése](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Jelentkezzen be a saját Access Portalra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![A hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package) | ![Külső felhasználói hozzáférés kérése](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>A csoportok, alkalmazások vagy SharePoint-webhelyek már van hozzáférésük megtekinteni kívánt

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Jelentkezzen be a saját Access Portalra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![A hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Aktív hozzáférési csomagok megtekintése |  |

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az első hozzáférési csomag létrehozása](entitlement-management-access-package-first.md)
- [Szerkesztése és kezelése a meglévő hozzáférési csomag](entitlement-management-access-package-edit.md)
