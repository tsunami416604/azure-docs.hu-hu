| Erőforrás | Basic | Standard | Prémium |
|---|---|---|---|---|
| Storage | 10 giB | 100 giB| 500 giB |
| Percenként ReadOps<sup>1, 2</sup> | 1k | 300-k | 10 000-k |
| Percenként WriteOps<sup>1, 3</sup> | 100 | 500 | 2-k |
| Töltse le a MB/s sávszélesség<sup>1</sup> | 30 | 60 | 100 |
| Töltse fel a MB/s sávszélesség<sup>1</sup> | 10 | 20 | 50 |
| webhook | 2 | 10 | 100 |
| Georeplikáció | N/A | N/A | [Támogatott *(előzetes verzió)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, és *sávszélesség* minimális hozzávetőlegesek. ACR nagy hangsúlyt fektet használati van szüksége a teljesítmény javítása érdekében.

<sup>2</sup> [docker lekéréses](https://docs.docker.com/registry/spec/api/#pulling-an-image) az eszköz több olvasási műveletek a lemezkép, valamint a jegyzék beolvasása rétegek száma alapján.

<sup>3</sup> [docker leküldéses](https://docs.docker.com/registry/spec/api/#pushing-an-image) az eszköz több írási műveleteket, rétegeket, amelyeket a leküldött értesítést kell száma alapján. A `docker push` tartalmaz *ReadOps* egy meglévő lemezképet jegyzékfájljának beolvasása.