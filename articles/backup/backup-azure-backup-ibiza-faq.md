<properties
   pageTitle="Azure Backup Nyilvános előzetes kiadása – GYIK | Microsoft Azure"
   description="A GYIK ezen verziója támogatja az Azure Backup szolgáltatás Nyilvános előzetes kiadását. Megválaszolja a biztonsági mentési ügynökre, a biztonsági mentésre és a megtartásra, helyreállításra, biztonságra és egyéb, a biztonsági mentéssel és az Azure Backup megoldással kapcsolatos általánosságokra vonatkozó gyakran ismételt kérdéseket."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="backup solution; backup service"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="03/30/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# Az Azure Backup szolgáltatás Nyilvános előzetes kiadása – GYIK

> [AZURE.SELECTOR]
- [Backup GYIK klasszikus módhoz](backup-azure-backup-faq.md)
- [Backup GYIK ARM módhoz](backup-azure-backup-ibiza-faq.md)

Ez a cikk az Azure Backup szolgáltatás Nyilvános előzetes kiadására vonatkozó konkrét információkat tartalmaz. Ez a cikk a gyakori kérdések beérkezésekor frissül, és kiegészíti az [Azure Backup GYIK](backup-azure-backup-faq) című cikket. Az Azure Backup GYIK tartalmazza az Azure Backup szolgáltatással kapcsolatos kérdéseket és válaszokat.  

Az Azure Backup szolgáltatással kapcsolatban ezen vagy egy kapcsolódó cikk Disqus szakaszában tehet fel kérdéseket. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Mi található a Nyilvános előzetes kiadásban?
A Nyilvános előzetes kiadás bevezeti a Recovery Services-tárolót, és ARM-támogatást nyújt Azure virtuális gépek megvédésekor. A Recovery Services-tároló a tárolók legújabb generációját képviseli. Ezt a tárolót használja az Azure és az Azure Site Recovery (ASR) szolgáltatás. Tekinthetjük v.2 tárolóként.

## Recovery Services és Backup tárolók

**1. kérdés Ha a Recovery Services-tárolók a v.2 tárolók, a Backup tárolók (v.1) még támogatottak?** <br/>
1. válasz Igen, a Backup tárolók még támogatottak. Backup tárolókat a klasszikus portálon lehet létrehozni. Recovery Services-tárolókat az Azure portálon lehet létrehozni.

**2. kérdés Áttelepíthetek egy Backup tárolót egy Recovery Services-tárolóra?** <br/>
2. válasz Sajnos nem, jelenleg nem telepíthető át egy Backup tároló tartalma egy Recovery Services-tárolóra. Jelenleg is dolgozunk ezen funkción, de a Nyilvános előzetes kiadásnak nem része.

**3. kérdés A Recovery Services-tárolók támogatják a virtuális gépek v.1 vagy v.2 változatait?** <br/>
 3. válasz A Recovery Services-tárolók támogatják a virtuális gépek v.1 és v.2 változatait. Biztonsági mentést végezhet a klasszikus portálon létrehozott virtuális gépekről (vagyis V.1), illetve az Azure portálon létrehozott virtuális gépekről (vagyis V.2) egy Recovery Services-tárolóba.


## ARM támogatás Azure virtuális gépekhez

**1. kérdés Az Azure virtuális gépek ARM támogatása korlátozott?** <br/>
1. válasz Az ARM-hez tartozó PowerShell-parancsmagok jelenleg nem érhetők el. Az Azure portál felhasználói felületét kell használni ahhoz, hogy erőforrásokat adjon hozzá egy erőforráscsoporthoz.



<!--HONumber=jun16_HO2-->


