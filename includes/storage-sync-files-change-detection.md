---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166037"
---
Az Azure-fájlmegosztás az Azure portal vagy az SMB használatával végzett módosítások azonnal nem észlelt, és például a kiszolgálói végpont módosításait replikálva. Az Azure Files még nem rendelkezik változási értesítéseket vagy a naplózást, így semmilyen módon nem lehet automatikusan elindítja a szinkronizálási munkamenet, amikor megváltoznak a fájlokat. Windows Server, az Azure File Sync használja [Windows USN naplózási](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) , automatikusan elindítja a szinkronizálási munkamenet, ha módosítja a fájlokat.<br /><br /> Az Azure-fájlmegosztás módosítások észleléséhez az Azure File Sync nevű egy ütemezett feladatot rendelkezik egy *észlelési feladat módosítása*. A módosítás észlelési feladat számba veszi a fájlmegosztás található összes fájlról, és összehasonlítja a Sync szolgáltatás verzióját, hogy a fájl. A módosítás észlelése határozza meg, hogy a fájlok változtak-e, ha az Azure File Sync egy szinkronizálási munkamenet indítja el. A módosítás észlelése 24 óránként indul. A módosítás észlelése számbavétele az Azure-fájlmegosztás az összes fájlt úgy működik, mert címváltozásának felderítését hosszabb időt vesz igénybe, mint a kisebb névterek nagyobb névterekben. A nagy névterek hosszabb, mint 24 óránként meghatározni, hogy mely fájlok változtak is eltarthat.<br /><br />
Vegye figyelembe, hogy Azure-fájlmegosztások REST használatával végzett módosítások nem az SMB utolsó módosítás időpontja nem a frissítést, és nem látható, a módosítás az sync. <br /><br />
Az Azure-fájlmegosztások kötet esetében USN hasonló hozzáadásának címváltozásának felderítését a Windows Server vizsgált azt. Segítsen rangsorolhatja jövőbeli fejlesztés ezt a funkciót a szavazzon [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
