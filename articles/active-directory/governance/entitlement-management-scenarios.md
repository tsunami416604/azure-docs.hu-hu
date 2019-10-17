---
title: Gyakori forgatókönyvek az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Ismerje meg a Azure Active Directory jogosultságok kezelése (előzetes verzió) gyakori forgatókönyveit.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81382ebecdff6c7b146386b3ae2b0768a7c834bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389068"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Gyakori forgatókönyvek az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Több módon is konfigurálhatja a jogosultságok kezelését a szervezet számára. Ha azonban csak most kezdi meg az első lépéseket, érdemes megismerni a rendszergazdák, a jóváhagyó és a kérelmező általános forgatókönyveit.

## <a name="administrators"></a>Rendszergazdák

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Új vagyok a jogosultságok felügyeletére, és segítségre van szükségem az első lépésekhez

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | [Az első hozzáférési csomag létrehozásához kövesse az oktatóanyagot](entitlement-management-access-package-first.md) | [@no__t – 1Azure portál ikon](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Szeretném engedélyezni a címtárban lévő felhasználóknak, hogy hozzáférést kérjenek a csoportokhoz, alkalmazásokhoz vagy SharePoint-webhelyekhez.

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [új hozzáférési csomag létrehozása katalógusban](entitlement-management-access-package-create.md#start-new-access-package) | ![Hozzáférési csomag létrehozása](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [erőforrás-Szerepkörök hozzáadása a csomaghoz való hozzáféréshez](entitlement-management-access-package-resources.md#add-resource-roles)<ul><li>Csoportok</li><li>Alkalmazások</li><li>SharePoint-webhelyek</li></ul> | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [szabályzat hozzáadása](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)<ul><li>A címtárban lévő felhasználók számára</li><li>Jóváhagyás megkövetelése</li><li>Életciklus-beállítások</li></ul> | ![Házirend hozzáadása](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Szeretném engedélyezni a felhasználók számára az üzleti partnerek címtárát (beleértve a címtárban még nem szereplő felhasználókat is), hogy hozzáférést kérjenek a csoportokhoz, alkalmazásokhoz vagy SharePoint-webhelyekhez.

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [új hozzáférési csomag létrehozása katalógusban](entitlement-management-access-package-create.md#start-new-access-package) | ![Hozzáférési csomag létrehozása](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [erőforrás-Szerepkörök hozzáadása a csomaghoz való hozzáféréshez](entitlement-management-access-package-resources.md#add-resource-roles) | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [szabályzat hozzáadása külső felhasználók számára](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)<ul><li>A címtárban nem szereplő felhasználók számára</li><li>Jóváhagyás megkövetelése</li><li>Életciklus-beállítások</li></ul> | ![Házirend hozzáadása a külső felhasználók számára](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [küldje el a hivatkozást, és kérjen hozzáférési csomagot az üzleti partnerének](entitlement-management-access-package-settings.md)<ul><li>Az üzleti partner megoszthatja a hivatkozásokat a felhasználókkal</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Egy hozzáférési csomagban szeretném módosítani a csoportokat, az alkalmazásokat és a SharePoint-helyeket

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** nyissa meg a hozzáférési csomagot | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [erőforrás-Szerepkörök hozzáadása vagy eltávolítása](entitlement-management-access-package-resources.md#add-resource-roles) | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Szeretném megtekinteni, hogy kik rendelkeznek hozzárendeléssel csoportokkal, alkalmazásokkal vagy SharePoint-webhelyekkel

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** hozzáférési csomag megnyitása | ![Erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [hozzárendelések megtekintése](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)<ul><li>Hozzáférési csomaghoz hozzáféréssel rendelkező felhasználók megtekintése</li><li>A felhasználó hozzáférésének megtekintése lejárt</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Meg szeretném tekinteni azokat a csoportokat, alkalmazásokat vagy SharePoint-webhelyeket, amelyekhez a felhasználónak hozzáférése van

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | [Felhasználói hozzárendelések jelentésének megtekintése](entitlement-management-reports.md)<ul><li>Megtekintés a kért és a jóváhagyott</li></ul> |  |

## <a name="approvers"></a>Jóváhagyók

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Szeretném jóváhagyni a csoportok, alkalmazások vagy SharePoint-webhelyek hozzáférésére vonatkozó kérelmeket

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [nyissa meg a kérelmet a saját hozzáférési portálon](entitlement-management-request-approve.md#open-request) | [@no__t – 1My hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [hozzáférési kérelem jóváhagyása](entitlement-management-request-approve.md#approve-or-deny-request) | ![Hozzáférés jóváhagyása](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Kérelmezők

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Szeretném megtekinteni a Microsoft számára elérhető csoportokat, alkalmazásokat vagy SharePoint-webhelyeket, és hozzáférést igényelni

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Jelentkezzen be a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t – 1My hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** hozzáférési csomag keresése |  |
> | **3.** [hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package) | ![Hozzáférés kérése](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Külső felhasználó vagyok, és szeretnék hozzáférést kérni a csoportokhoz, alkalmazásokhoz vagy SharePoint-helyekhez közvetlen kapcsolattal

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Keresse meg a kapott hozzáférési csomag hivatkozását](entitlement-management-access-package-settings.md) |  |
> | **2.** [Jelentkezzen be a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t – 1My hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package) | ![Hozzáférés kérése külső felhasználó számára](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Szeretném megtekinteni azokat a csoportokat, alkalmazásokat vagy SharePoint-webhelyeket, amelyeknek már van hozzáférése

> [!div class="mx-tableFixed"]
> | Lépések | Példa |
> | --- | --- |
> | **1.** [Jelentkezzen be a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t – 1My hozzáférési portál ikon](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** aktív hozzáférési csomagok megtekintése |  |

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: az első hozzáférési csomag létrehozása](entitlement-management-access-package-first.md)
- [Delegálás és szerepkörök](entitlement-management-delegate.md)
