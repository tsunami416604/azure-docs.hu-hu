## Mi a Queue Storage?

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat.

A Queue Storage gyakori használati módjai:

-   Hátralékos munkák létrehozása aszinkron feldolgozáshoz
-   Üzenetek átadása egy Azure webes szerepkörről egy Azure feldolgozói szerepkörnek

## A Queue szolgáltatás alapfogalmai

A Queue szolgáltatás az alábbi összetevőkből áll:

![Queue1](./media/storage-queue-concepts-include/queue1.png)


- **URL-formátum:** Az üzenetsorok a következő URL-formátummal érhetők el:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
        
        http://myaccount.queue.core.windows.net/images-to-download

- **Tárfiók:** Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](../articles/storage/storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

- **Üzenetsor:** Az üzenetsorok üzenetek készleteit tartalmazzák. Az összes üzenetnek üzenetsorban kell lennie. Vegye figyelembe, hogy az üzenetsor neve csak kisbetűket tartalmazhat. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).

- **Üzenet:** Egy legfeljebb 64 KB méretű, tetszőleges méretű üzenet. Egy üzenet legfeljebb 7 napig maradhat egy üzenetsorban.



<!--HONumber=Jun16_HO2-->


