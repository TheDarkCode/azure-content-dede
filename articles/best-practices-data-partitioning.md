<properties
   pageTitle="Anleitungen zur Datenpartitionierung | Microsoft Azure"
   description="Leitfaden zum Trennen von Partitionen für die separate Verwaltung und den separaten Zugriff"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="masashin"/>

# Leitfaden zur Datenpartitionierung

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## Übersicht

In vielen umfangreichen Lösungen werden Daten in getrennte Partitionen aufgeteilt, die getrennt verwaltet werden können und auf die separat zugegriffen werden kann. Die Partitionierungsstrategie muss sorgfältig ausgewählt werden, um die Vorteile zu maximieren und gleichzeitig nachteilige Auswirkungen zu minimieren. Partitionierung kann die Skalierbarkeit verbessern, Konflikte reduzieren und die Leistung optimieren. Ein weiterer Vorteil der Partitionierung ist, dass sie einen Mechanismus zum Unterteilen von Daten nach Verwendungsmuster bereitstellen kann. Beispielsweise können Sie ältere, weniger aktive (kalte) Daten in kostengünstigeren Datenspeichern archivieren.

## Gründe für Datenpartitionierung

Die meisten Cloud-Anwendungen und Dienste speichern Daten als Teil ihrer Vorgänge ab und rufen diese auf. Das Design der Datenspeicher, die eine Anwendung nutzt, kann einen erheblichen Einfluss auf die Leistung, den Durchsatz und die Skalierbarkeit eines Systems haben. Eine Technik, die häufig für großmaßstäbliche Systeme eingesetzt wird, ist die Unterteilung von Daten in separate Partitionen.

> Der in diesem Leitfaden verwendete Begriff _Partitionierung_ bezieht sich auf den physischen Prozess der Datenunterteilung in separate Datenspeicher. Dies ist nicht identisch mit der SQL Server-Tabellenpartitionierung, die ein anderes Konzept darstellt.

Partitionieren von Daten kann eine Reihe von Vorteilen bieten. Es kann z. B. angewendet werden für:

- **Verbesserung der Skalierbarkeit**. Beim zentralen Hochskalieren eines einzelnen Datenbanksystems wird schließlich ein physisches Hardwarelimit erreicht. Wenn Sie Daten auf mehrere Partitionen aufteilen, von denen jede auf einem separaten Server gehostet wird, können Sie das System fast unbegrenzt horizontal hochskalieren.
- **Verbesserung der Leistung** Zugriffe auf Daten in jeder Partition finden über eine kleinere Datenmenge statt. Vorausgesetzt, dass die Daten auf geeignete Weise partitioniert werden, wird Ihr System dadurch viel effizienter. Vorgänge, die mehr als eine Partition betreffen, können parallel ausgeführt werden. Jede Partition kann in der Nähe der Anwendung gefunden werden, die diese verwendet, um die Netzwerklatenz zu minimieren.
- **Verbesserung der Verfügbarkeit**. Das Aufteilen von Daten über mehrere Server hinweg vermeidet eine einzelne Fehlerquelle. Wenn ein Server ausfällt oder planmäßig gewartet wird, sind nur die Daten in der Partition nicht verfügbar. Vorgänge auf anderen Partitionen können fortgesetzt werden. Durch Erhöhen der Anzahl von Partitionen verringern sich die relativen Auswirkungen des Ausfalls eines einzelnen Servers, da der Prozentsatz an Daten reduziert wird, die nicht verfügbar sind. Das Replizieren jeder Partition kann das Risiko eines einzelnen Partitionsausfalls, der sich auf die Vorgänge auswirkt, reduzieren. Auch können kritische Daten, die dauerhaft hochverfügbar sein müssen, von Daten mit geringerem Wert (z. B. Protokolldateien) getrennt werden, die geringere Verfügbarkeitsanforderungen haben.
- **Verbesserung die Sicherheit**. Abhängig von der Art der Daten und ihrer Partitionierung können sensible und nicht sensible Daten in verschiedenen Partitionen und somit auf unterschiedlichen Servern bzw. in unterschiedlichen Datenspeichern gespeichert werden. Die Sicherheit kann dann speziell für die sensiblen Daten optimiert werden.
- **Bereitstellen von Flexibilität bei Vorgängen**. Partitionieren bietet viele Möglichkeiten für die Feinabstimmung von Vorgängen, das Maximieren administrativer Effizienz und die Minimierung von Kosten. Sie können beispielsweise basierend auf der Wichtigkeit der Daten in jeder Partition unterschiedliche Strategien für die Verwaltung, Überwachung, Sicherung und Wiederherstellung sowie für andere administrative Aufgaben definieren.
- **Übereinstimmung der Daten mit dem Anwendungsmuster**. Mithilfe der Partitionierung kann jede Partition basierend auf den Kosten und integrierten Funktionen, die dieser Datenspeicher bietet, auf einer anderen Art von Datenspeicher bereitgestellt werden. Umfangreiche binäre Daten können z. B. in einem Blobdatenspeicher gespeichert werden, strukturiertere Daten dagegen in einer Dokumentendatenbank. Weitere Informationen finden Sie im Patterns & Practices-Handbuch auf der Microsoft-Website unter [Building a polyglot solution] (Erstellen einer Polyglot-Lösung) sowie unter [Data access for highly-scalable solutions: Using SQL, NoSQL, and polyglot persistence] (Datenzugriff für hoch skalierbare Lösungen: mit SQL, NoSQL und Polyglot Persistence).

Einige Systeme implementieren keine Partitionierung, da sie als aufwendig anstatt als vorteilhaft empfunden wird. Häufige Erklärungen für diese Begründung umschließen:

- Viele Datenspeichersysteme unterstützen keine Verknüpfungen über Partitionen hinweg und es kann schwierig sein, in einem partitionierten System referenzielle Integrität zu wahren. Es ist häufig erforderlich, Verknüpfungen zu implementieren und Integritätsprüfungen im Anwendungscode (in der Partitionierungsebene) vorzunehmen, was zu zusätzlichen E/A und höherer Anwendungskomplexität führen kann.
- Verwalten von Partitionen ist nicht immer eine einfache Aufgabe. In einem System, in dem die Daten flüchtig sind, müssen Sie die Partitionen in regelmäßigen Abständen neu verteilen, um Konflikte und Hotspots zu reduzieren.
- Einige häufig verwendete Tools funktionieren mit partitionierten Daten nicht natürlich.

## Entwerfen von Partitionen

Daten können auf unterschiedliche Weise partitioniert werden: horizontal, vertikal oder funktional. Die von Ihnen gewählte Strategie hängt von dem Grund für die Partitionierung der Daten sowie von den Anforderungen der Anwendungen und Dienste ab, die die Daten verwenden werden.

> [AZURE.NOTE] Die in diesem Leitfaden beschriebenen Partitionierungsschemen werden auf eine Weise erläutert, die von der zugrunde liegenden Datenspeicherungstechnologie unabhängig ist. Sie können auf viele Arten von Datenspeichern, einschließlich relationalen und NoSQL-Datenbanken, angewendet werden.

### Partitionierungsstrategien

Die drei typischen Strategien zum Partitionieren von Daten sind:

- **Horizontale Partitionierung** (oft _Sharding_ genannt). Bei dieser Strategie stellt jede Partition einen selbstständigen Datenspeicher dar, wobei jedoch alle Partitionen das gleiche Schema aufweisen. Jede Partition ist als ein _Shard_ bekannt und enthält eine bestimmte Teilmenge der Daten, wie z. B. alle Bestellungen für einen bestimmten Satz von Kunden in einer E-Commerce-Anwendung.
- **Vertikale Partitionierung**. Bei dieser Strategie enthält jede Partition eine Teilmenge der Felder für Elemente im Datenspeicher. Die Felder werden gemäß ihrem Verwendungsmuster unterteilt. Beispielsweise können häufig verwendete Felder in einer vertikalen Partition und weniger häufig verwendete Felder in einer anderen Partition platziert werden.
- **Funktionale Partitionierung**. Bei dieser Strategie werden Daten nach der Art und Weise aggregiert, in der sie im System von jedem begrenzten Kontext verwendet werden. Ein Beispiel: In einem E-Commerce-System, das separate Geschäftsfunktionen für die Rechnungsstellung und die Verwaltung des Warenbestands implementiert, werden Rechnungsdaten möglicherweise in einer Partition und Daten zum Warenbestand in einer anderen Partition gespeichert.

Es ist wichtig zu beachten, dass die hier beschriebenen Strategien kombiniert werden können. Sie schließen sich nicht gegenseitig aus, und es empfiehlt sich, sie alle beim Entwurf eines Partitionierungsschemas zu berücksichtigen. Beispielsweise könnten Sie Daten in Shards unterteilen und dann die Daten mittels vertikaler Partitionierung innerhalb der einzelnen Shards weiter unterteilen. Auf ähnliche Weise können die Daten einer funktionalen Partition in Shards aufgeteilt werden (die ebenfalls vertikal partitioniert werden können).

Die unterschiedlichen Anforderungen der einzelnen Strategien können jedoch eine Reihe von in Konflikt stehenden Problemen auslösen. Sie müssen all diese möglichen Probleme bewerten und gegeneinander abwägen, wenn Sie ein Partitionierungsschema entwerfen, das sämtliche Leistungsziele der Datenverarbeitung für Ihr System erfüllen soll. Die folgenden Abschnitte untersuchen jede einzelne Strategien eingehender.

### Horizontale Partitionierung (Sharding)

Abbildung 1 zeigt eine Übersicht der horizontalen Partitionierung oder Sharding. In diesem Beispiel werden Warenbestandsdaten basierend auf dem Produktschlüssel in Shards unterteilt. Jedes Shard enthält die Daten für einen zusammenhängenden Bereich von Shard-Schlüsseln (A-G und H-Z) in alphabetischer Anordnung.

![Horizontale Partitionierung (Sharding) von Daten auf Grundlage eines Partitionsschlüssels](media/best-practices-data-partitioning/DataPartitioning01.png)

_Abbildung 1: Horizontale Partitionierung (Sharding) von Daten auf Grundlage eines Partitionsschlüssels_

Durch Sharding können Sie die Last auf mehrere Computer verteilen, was Konflikte reduziert und die Leistung verbessert. Sie können das System horizontal hochskalieren, indem Sie weitere Shards hinzufügen, die auf zusätzlichen Servern ausgeführt werden.

Der wichtigste Faktor bei der Implementierung dieser Partitionierungsstrategie ist die Auswahl der Shardingschlüssel. Es kann schwierig sein, den Schlüssel zu ändern, nachdem das System in Betrieb ist. Der Schlüssel muss sicherstellen, dass Daten auf eine Weise partitioniert werden, die die Workload möglichst gleichmäßig über die Shards hinweg verteilt.

Beachten Sie, dass verschiedene Shards keine ähnliche Datenmengen enthalten müssen. Wichtiger ist es, die Anzahl von Anfragen gleichmäßig zu verteilen. Einige Shards sind möglicherweise sehr groß, aber es gibt nur eine geringe Anzahl von Zugriffsvorgängen für jedes Element. Andere Shards können kleiner sein, aber es wird viel häufiger auf jedes Element zugegriffen. Außerdem ist es wichtig sicherzustellen, dass kein einzelner Shard die Skalierungsgrenzen (im Hinblick auf Kapazität und Verarbeitungsressourcen) des Datenspeichers überschreitet, der zum Hosten dieses Shards verwendet wird.

Wenn Sie ein Shardingschema verwenden, sollten Sie auch vermeiden, Hotspots (oder „heiße“ Partitionen) zu erstellen, die möglicherweise die Leistung und Verfügbarkeit beeinträchtigen. Beispielsweise verhindert das Verwenden eines Hashs einer Kunden-ID anstelle des ersten Buchstaben des Kundennamens eine ungleichmäßige Verteilung, die durch häufige und weniger häufige Anfangsbuchstaben entsteht. Dies ist ein übliches Verfahren, mit dessen Hilfe Daten gleichmäßiger über die Partitionen verteilt werden können.

Wählen Sie einen Shardingschlüssel, der die Notwendigkeit minimiert, zu einem späteren Zeitpunkt große Shards in kleinere Teile aufteilen, kleinere Shards in größeren Partitionen zusammenfügen oder das Schema ändern zu müssen, das die in einem Satz von Partitionen gespeicherten Daten beschreibt. Diese Vorgänge können sehr zeitaufwendig sein und erfordern es möglicherweise, während der Ausführung einen oder mehrere Shards offline zu schalten.

Wenn Shards repliziert werden, ist es eventuell möglich, einige der Replikate online zu halten, während andere Replikate geteilt, zusammengeführt oder neu konfiguriert werden. Das System muss jedoch möglicherweise die Vorgänge begrenzen, die für die Daten in diesen Shards ausgeführt werden können, während die Neukonfiguration stattfindet. Beispielsweise können die Daten in den Replikaten als schreibgeschützt markiert werden, um den Umfang von Inkonsistenzen zu begrenzen, die bei der Umstrukturierung von Shards auftreten können.

> Ausführlichere Informationen und Anweisungen zu vielen dieser Überlegungen sowie zu bewährten Verfahren für das Entwerfen von Datenspeichern, die horizontale Partitionierung implementieren, finden Sie unter [Sharding Pattern] \(Shardingmuster).

### Vertikale Partitionierung

Die häufigste Verwendung für die vertikale Partitionierung ist, die E/A und die Leistungskosten zu reduzieren, die mit dem Abrufen der Elemente verknüpft sind, auf die am häufigsten zugegriffen wird. Abbildung 2 zeigt ein Beispiel für die vertikale Partitionierung. In diesem Beispiel sind verschiedene Eigenschaften für jedes Datenelement in verschiedenen Partitionen enthalten. Eine Partition enthält Daten, auf die häufiger zugegriffen wird, einschließlich Name, Beschreibung und Preisinformationen für Produkte. Eine andere Partition enthält die Menge im Warenbestand und das Datum der letzten Bestellung.

![Vertikale Partitionierung von Daten nach Verwendungsmuster](media/best-practices-data-partitioning/DataPartitioning02.png)

_Abbildung 2: Vertikale Partitionierung von Daten nach Verwendungsmuster_

In diesem Beispiel fragt die Anwendung regelmäßig den Produktnamen, die Beschreibung und den Preis ab, wenn Kunden Produktdetails angezeigt werden. Die Lagerbestand und das Datum, an dem das Produkt zuletzt vom Hersteller bestellt wurde, sind in einer separaten Partition enthalten, da diese beiden Elemente häufig zusammen verwendet werden.

Dieses Partitionierungsschema bietet den zusätzlichen Vorteil, dass sich relativ langsam bewegende Daten (Produktname, Beschreibung und Preis) von den dynamischeren Daten (Lagerbestand und Datum der letzten Bestellung) getrennt werden. Für eine Anwendung ist es möglicherweise sinnvoll, die sich langsam bewegenden Daten im Arbeitsspeicher zwischenzuspeichern, wenn auf diese regelmäßig zugegriffen wird.

Ein weiteres typisches Szenario für diese Partitionierungsstrategie ist die Maximierung der Sicherheit sensibler Daten. Dies können Sie z. B. durch Speichern von Kreditkartennummern und der entsprechenden Kartenprüfnummern in separaten Partitionen implementieren.

Eine vertikale Partitionierung kann auch die erforderlichen gleichzeitigen Zugriffe auf die Daten verringern.

> Vertikale Partitionierung findet auf der Entitätsebene in einem Datenspeicher statt, wobei eine Entität teilweise normalisiert wird, um sie von einem _breiten_ Element in einen Satz _schmaler_ Elemente aufzuschlüsseln. Es eignet sich ideal für spaltenorientierte Datenspeicher wie z. B. HBase und Cassandra. Wenn es unwahrscheinlich ist, dass sich Daten in einer Auflistung von Spalten ändern, können Sie auch in Betracht ziehen, Spaltenspeicher im SQL Server zu nutzen.

### Funktionale Partitionierung

Für Systeme, in denen es möglich ist, einen gebundenen Kontext für jeden bestimmten Geschäftsbereich oder Dienst in der Anwendung zu identifizieren, bietet die funktionale Partitionierung ein Verfahren zur Verbesserung der Isolation und Datenzugriffsleistung. Darüber hinaus wird die funktionale Partitionierung häufig verwendet, um Lese/Schreib-Daten von schreibgeschützten Daten zu trennen, die für Berichtszwecke verwendet werden. Abbildung 3 zeigt eine Übersicht einer funktionalen Partitionierung, in der Bestandsdaten von Kundendaten getrennt werden.

![Funktionale Datenpartitionierung nach begrenztem Kontext oder Unterdomäne](media/best-practices-data-partitioning/DataPartitioning03.png)

_Abbildung 3: Funktionale Datenpartitionierung nach begrenztem Kontext oder Unterdomäne_

Diese Partitionierungsstrategie kann helfen, Datenzugriffskonflikte über verschiedene Teile eines Systems hinweg zu reduzieren.

## Entwerfen von Partitionen für Skalierbarkeit

Es ist wichtig, die Größe und Workload für jede Partition zu berücksichtigen und auszubalancieren, um die Daten so zu verteilen, dass eine maximale Skalierbarkeit erreicht wird. Allerdings müssen Sie die Daten auch so partitionieren, dass nicht die Skalierungsgrenzen des Speicherplatzes der einzelnen Partitionen überschritten werden.

Gehen Sie beim Entwerfen von Partitionen für maximale Skalierbarkeit folgendermaßen vor:

1. Analysieren Sie die Anwendung, um die Datenzugriffsmuster zu verstehen, wie z. B. die Größe des Resultsets jeder Abfrage, die Häufigkeit des Zugriffs, die inhärente Latenz und die serverseitige Anforderungen an die Rechenleistung. In vielen Fällen werden einige Hauptentitäten den Großteil der Verarbeitungsressourcen anfordern.
2. Verwenden Sie diese Analyse, um die aktuellen und zukünftigen Skalierbarkeitsziele wie z. B. die Größe der Daten und die Workload festzulegen. Anschließend verteilen Sie die Daten auf die Partitionen, um das Skalierbarkeitsziel zu erreichen. Für die Strategie zur horizontalen Partitionierung ist die Auswahl des angemessenen Shardschlüssels wichtig, um sicherzustellen, dass die Verteilung gleichmäßig erfolgt ist. Weitere Informationen finden Sie unter [Sharding Pattern] \(Shardingmuster).
3. Stellen Sie sicher, dass die für jede Partition verfügbaren Ressourcen ausreichen, um den Anforderungen an die Skalierbarkeit im Hinblick auf die Größe der Daten und den Durchsatz zu entsprechen. Beispielsweise erzwingt der Knoten, der eine Partition hostet, möglicherweise eine harte Grenze für die Menge an Speicherplatz, Verarbeitungsleistung oder Netzwerkbandbreite, die er bereitstellt. Wenn die Datenspeicher- und Verarbeitungsanforderungen diese Grenzwerte wahrscheinlich überschreiten werden, kann es notwendig sein, Ihre Partitionierungsstrategie zu verfeinern oder die Daten noch weiter aufzuteilen. Eine Herangehensweise an die Skalierbarkeit kann es z. B. sein, Protokolldaten von den Kernfunktionen der Anwendung zu trennen. Dazu verwenden Sie getrennte Datenspeicher, um zu verhindern, dass die Datenspeicheranforderungen insgesamt die Skalierungsgrenzwerte des Knotens überschreiten. Wenn die Gesamtzahl der Datenspeicher die Grenzwerte des Knotens überschreitet, kann es notwendig sein, separate Speicherknoten zu verwenden.
4. Überwachen Sie das verwendete System, um sicherzustellen, dass die Daten wie erwartet verteilt werden und die Partitionen die vorgegebene Last verarbeiten können. Es ist möglich, dass die tatsächliche Nutzung nicht der durch die Analyse prognostizierten Nutzung entspricht. In diesem Fall können die Partitionen möglicherweise neu aufgeteilt werden. Sollte dies nicht möglich sein, müssen eventuell einige Teile des Systems umgestaltet werden, um die erforderliche Balance zu erzielen.

Beachten Sie, dass einige Cloudumgebungen Ressourcen gemäß Infrastrukturgrenzen zuordnen. Stellen Sie sicher, dass die Limits der von Ihnen ausgewählten Infrastrukturgrenze hinsichtlich Datenspeicherung, Verarbeitungsleistung und Bandbreite ausreichend Platz für das erwartete Wachstum der Datenmenge bieten.

Wenn Sie beispielsweise den Azure-Tabellenspeicher verwenden, benötigt ein ausgelasteter Shard möglicherweise mehr Ressourcen, als in einer einzelnen Partition zur Verarbeitung von Anforderungen zur Verfügung stehen. (Die Menge von Anforderungen, die von einer einzelnen Partition in einem bestimmten Zeitraum verarbeitet werden können, ist begrenzt. Weitere Informationen finden Sie auf der Microsoft-Website im Artikel [Skalierbarkeits- und Leistungsziele für Azure Storage].)

 In diesem Fall muss der Shard womöglich neu partitioniert werden, um die Last zu verteilen. Wenn die Gesamtgröße oder der Gesamtdurchsatz dieser Tabellen die Kapazität eines Speicherkontos überschreitet, kann das Erstellen zusätzlicher Speicherkonten sowie die Verteilung der Tabellen auf diese Konten erforderlich sein. Überschreitet die Anzahl der Speicherkonten die Anzahl der Konten, die für ein Abonnement verfügbar sind, müssen möglicherweise mehrere Abonnements verwendet werden.

## Entwerfen von Partitionen für die Abfrageleistung

Die Abfrageleistung kann oft mit kleineren Datensätzen und der parallelen Ausführung von Abfragen erhöht werden. Jede Partition sollte einen kleinen Anteil des gesamten Datasets enthalten. Diese Reduzierung des Volumens kann die Leistung von Abfragen verbessern. Allerdings ist das Partitionieren keine Alternative zum angemessenen Entwerfen und Konfigurieren einer Datenbank. Stellen Sie z. B. sicher, dass Sie über die erforderlichen Indizes verfügen, wenn Sie eine relationale Datenbank verwenden.

Gehen Sie beim Entwerfen von Partitionen für maximale Abfrageleistung folgendermaßen vor:

1. Überprüfen Sie die Anwendungsanforderungen und Leistung:
	- Berücksichtigen Sie die Unternehmensanforderungen, um die kritischen Anfragen zu bestimmen, die stets schnell ausgeführt werden müssen.
	- Überwachen Sie das System, um Abfragen zu identifizieren, die nur langsam durchgeführt werden.
	- Ermitteln Sie, welche Abfragen am häufigsten ausgeführt werden. Eine einzelne Instanz einer Abfrage kann minimale Kosten haben, aber der kumulative Ressourcenverbrauch könnte erheblich sein. Es kann von Vorteil sein, die Daten, die durch diese Abfragen abgerufen werden, in eine bestimmte Partition oder sogar einen Cache zu separieren.
2. Partitionieren Sie die Daten, die für eine geringe Leistung verantwortlich sind:
	- Beschränken Sie die Größe der einzelnen Partitionen, sodass die Abfrageantwortzeit innerhalb des Ziels liegt.
	- Entwerfen Sie den Shardschlüssel so, dass die Anwendung die Partition problemlos finden kann, wenn Sie eine horizontale Partitionierung implementieren. Dies verhindert, dass die Abfrage jede Partition durchsuchen muss.
	- Berücksichtigen Sie den Speicherort einer Partition. Versuchen Sie nach Möglichkeit die Daten in Partitionen zu belassen, die geografisch dicht bei den Anwendungen und Benutzern liegen, die darauf zugreifen.
3. Besitzt eine Entität Durchsatz und Anforderungen an die Abfrageleistung, verwenden Sie funktionale Partitionierung basierend auf dieser Entität. Wenn die Anforderungen dadurch immer noch nicht erfüllt sind, wenden Sie auch eine horizontale Partitionierung an. In den meisten Fällen reicht eine einzelne Partitionierungsstrategie, aber in einigen Fällen ist es effizienter, beide Strategien zu kombinieren.
4. Erwägen Sie asynchrone Abfragen, die zur Verbesserung der Leistung über Partitionen hinweg parallel ausgeführt werden.

## Entwerfen von Partitionen für Verfügbarkeit

Partitionieren von Daten kann die Verfügbarkeit von Anwendungen verbessern, indem Sie sicherstellen, dass das gesamte Dataset keine einzelne Fehlerquelle darstellt und einzelne Teilmengen des Datasets unabhängig voneinander verwaltet werden können. Das Replizieren von Partitionen mit wichtigen Daten kann die Verfügbarkeit auch verbessern.

Berücksichtigen Sie beim Entwerfen und Implementieren von Partitionen die folgenden Faktoren, die sich auf die Verfügbarkeit auswirken:

- **Wie kritisch sind die Daten für den Geschäftsbetrieb?** Einige Daten können kritische geschäftliche Informationen wie z. B. Rechnungsdetails oder Banktransaktionen umfassen. Andere Daten enthalten möglicherweise weniger kritische operative Informationen, wie Protokolldateien, Daten zur Leistungsnachverfolgung usw. Berücksichtigen Sie nach dem Ermitteln der jeweiligen Daten Folgendes:
	- Speichern von kritischen Daten in hochverfügbaren Partitionen mit einem geeigneten Sicherungsplans.
	- Einrichten von separater Verwaltung und Überwachungsmechanismen oder Verfahren für die verschiedenen Kritikalitäten von jedem Dataset. Speichern Sie Daten der gleichen Wichtigkeitsstufe in der gleichen Partition, sodass sie mit angemessener Häufigkeit zusammen gesichert werden können. Partitionen, die Daten für Banktransaktionen enthalten, müssen z. B. häufiger gesichert werden als Partitionen mit Protokollierungs- oder Nachverfolgungsinformationen.
- **Wie werden die einzelnen Partitionen verwaltet?** Das Entwerfen von Partitionen, um unabhängige Verwaltung und Wartung zu unterstützen, bietet mehrere Vorteile. Zum Beispiel:
	- Wenn eine Partition fehlschlägt, kann sie unabhängig wiederhergestellt werden, ohne Auswirkungen auf Instanzen von Anwendungen zu haben, die auf Daten in anderen Partitionen zugreifen.
	- Das Partitionieren von Daten nach geografischem Bereich ermöglicht die Ausführung von geplanten Wartungsaufgaben außerhalb der Spitzenzeiten für jeden Standort. Stellen Sie sicher, dass die Partitionen nicht zu groß sind, um zu verhindern, dass eine geplante Wartung während dieses Zeitraums abgeschlossen wird.
- **Sollen kritische Daten partitionsübergreifend repliziert werden oder nicht?** Diese Strategie kann Verfügbarkeit und Leistung verbessern, obwohl es auch zu Konsistenzproblemen führen kann. Es dauert eine Weile, bis Änderungen an Daten in einer Partition mit jedem Replikat synchronisiert wurden. Während dieses Zeitraums enthalten verschiedene Partitionen unterschiedliche Datenwerte.

## Grundlegendes zu den Auswirkungen der Partitionierung auf Entwurf und Entwicklung

Durch Partitionierung werden der Entwurf und die Entwicklung des Systems komplexer. Betrachten Sie die Partitionierung als grundlegenden Bestandteil des Systementwurfs, selbst wenn das System zunächst nur eine Partition umfasst. Wenn Sie eine Partitionierung erst dann vornehmen, wenn das System bereits Leistungs- und Skalierbarkeitsprobleme zeigt, steigt die Komplexität, da Sie mittlerweile ein Live-System zu verwalten haben.

Wenn Sie das System in dieser Umgebung partitionieren, müssen Sie die Datenzugriffslogik ändern. Dazu kann auch gehören, dass Sie große Mengen bereits vorhandener Daten migrieren müssen, um sie auf die Partitionen zu verteilen – dies kann zu Konflikten führen, da die Benutzer erwarten, das System weiterhin nutzen zu können.

In einigen Fällen wird die Partitionierung nicht als wichtig angesehen, da das ursprüngliche Dataset klein ist und problemlos von einem einzelnen Server verarbeitet werden kann. Dies mag für ein System zutreffen, das voraussichtlich nicht über seine Anfangsgröße hinaus skaliert werden muss, viele kommerzielle Systeme müssen jedoch mit zunehmender Benutzeranzahl erweitert werden. Diese Erweiterung wird in der Regel von einer Zunahme der Datenmenge begleitet.

Es ist auch wichtig zu verstehen, dass die Partitionierung nicht immer eine Funktion von großen Datenspeichern ist. Beispielsweise kann auf einen kleinen Datenspeicher von mehreren hundert gleichzeitigen Clients intensiv zugegriffen werden. Das Partitionieren der Daten kann in dieser Situation helfen, Konflikte zu minimieren und den Durchsatz verbessern.

Beachten Sie beim Entwerfen eines Schemas für die Datenpartitionierung folgende Punkte:

- **Halten Sie die Daten für die am häufigsten verwendeten Datenbankvorgänge nach Möglichkeit in jeder Partition zusammen, um partitionsübergreifende Datenzugriffsvorgänge zu minimieren.** Abfragen über Partitionen hinweg kann mehr Zeit in Anspruch nehmen als Abfragen innerhalb einer einzelnen Partition. Das Optimieren der Partitionen für eine Reihe von Abfragen könnte jedoch andere Sätze von Abfragen beeinträchtigen. Wenn sich partitionsübergreifende Abfragen nicht vermeiden lassen, minimieren Sie die Abfragezeit, indem Sie parallele Abfragen ausführen und die Ergebnisse innerhalb der Anwendung aggregieren. Diese Herangehensweise lässt sich möglicherweise nicht in allen Fällen umsetzen, z. B. wenn ein Ergebnis aus einer bestimmten Abfrage für die nächste Abfrage verwendet werden muss.
- **Wenn Abfragen relativ statische Referenzdaten nutzen, z. B. Tabellen mit Postleitzahlen oder Produktlisten, sollten Sie erwägen, diese Daten in allen Partitionen zu replizieren, um die Anforderungen für separate Suchvorgänge in den verschiedenen Partitionen zu reduzieren.** Dadurch lässt sich auch die Wahrscheinlichkeit verringern, dass Referenzdaten zu einem „heißen“ Dataset mit umfangreichem Datenverkehr aus dem gesamten System werden. Das Synchronisieren von Änderungen, die an diesen Referenzdaten vorgenommen werden, ist jedoch mit zusätzlichem Aufwand verbunden.
- **Minimieren Sie nach Möglichkeit die Anforderungen für referenzielle Integrität über vertikale und funktionale Partitionen hinweg.** In diesen Schemen ist die Anwendung selbst für die Wahrung der referenziellen Integrität über Partitionen hinweg verantwortlich, wenn die Daten aktualisiert und verarbeitet werden. Abfragen, die Daten über mehrere Partitionen hinweg verknüpfen müssen, werden langsamer ausgeführt als Abfragen, die nur Daten in der gleichen Partition verknüpfen, da die Anwendung in der Regel aufeinanderfolgende Abfragen basierend auf einem Schlüssel und dann auf einem Fremdschlüssel durchführen muss. Ziehen Sie stattdessen in Betracht, die relevanten Daten zu replizieren oder zu denormalisieren. Um die Zeit für Abfragen zu minimieren, bei denen partitionsübergreifende Verknüpfungen notwendig sind, führen Sie parallele Abfragen über die Partitionen hinweg aus, und verknüpfen Sie die Daten innerhalb der Anwendung.
- **Wägen Sie die Auswirkungen ab, die das Partitionierungsschema möglicherweise auf die Datenkonsistenz über die Partitionen hinweg hat.** Überprüfen Sie, ob eine hohe Konsistenz tatsächlich erforderlich ist. Stattdessen ist eine gängige Vorgehensweise in der Cloud, letztendliche Konsistenz zu implementieren. Die Daten in jeder Partition werden separat aktualisiert, und die Anwendungslogik stellt sicher, dass alle Updates erfolgreich abgeschlossen werden. Die Logik verarbeitet auch alle Inkonsistenzen, die durch Abfragen von Daten während eines letztlich konsistenten Vorgangs entstehen. Weitere Informationen zum Implementieren von letztlicher Konsistenz finden Sie unter [Data Consistency Primer] (Grundlagen der Datenkonsistenz).
- **Überlegen Sie, wie Abfragen die richtige Partition finden.** Wenn eine Abfrage alle Partitionen durchsuchen muss, um die erforderlichen Daten zu finden, wirkt sich das erheblich auf die Leistung aus, auch wenn mehrere parallele Abfragen ausgeführt werden. Abfragen, die mit vertikalen und funktionalen Partitionierungsstrategien verwendet werden, können natürlich die Partitionen angeben. Allerdings kann bei Verwendung der horizontalen Partitionierung (Sharding) das Auffinden eines Elements schwierig sein, da jeder Shard über das gleiche Schema verfügt. Eine typische Lösung für Sharding ist, eine Zuordnung zu verwalten, die verwendet werden kann, um den Shard-Speicherort für bestimmte Datenelemente zu suchen. Diese Zuordnung kann in der Shardinglogik der Anwendung implementiert sein oder vom Datenspeicher verwaltet werden, wenn transparentes Sharding unterstützt wird.
- **Bei Verwendung einer horizontalen Partitionierungsstrategie sollten Sie einen regelmäßigen Neuausgleich der Shards erwägen.** So lassen sich die Daten nach Größe und Workload gleichmäßig verteilen, um Hotspots zu minimieren, die Abfrageleistung zu maximieren und physische Speichereinschränkungen zu umgehen. Dies ist jedoch eine komplexe Aufgabe, die häufig ein benutzerdefiniertes Tool oder einen benutzerdefinierten Prozess erfordert.
- **Die Replikation jeder Partition bietet zusätzlichen Schutz vor Ausfällen.** Wenn ein einzelnes Replikat fehlschlägt, können Abfragen gegen eine Arbeitskopie geleitet werden.
- **Wenn die physischen Grenzen einer Partitionierungsstrategie erreicht sind, müssen Sie die Skalierbarkeit auf eine andere Ebene erweitern.** Wurde die Partitionierung beispielsweise auf Datenbankebene implementiert, müssen Sie möglicherweise Partitionen in mehreren Datenbanken suchen oder replizieren. Wenn die Partitionierung bereits auf Datenbankebene implementiert wurde und physische Einschränkungen ein Problem sind, kann dies bedeuten, dass Sie Partitionen in mehreren Hostingkonten suchen oder replizieren müssen.
- **Vermeiden Sie Transaktionen, die auf Daten in mehreren Partitionen zugreifen.** Einige Datenspeicher implementieren Transaktionskonsistenz und -integrität für Vorgänge, die Daten ändern, aber nur, wenn sich diese in einer einzelnen Partition befinden. Wenn Sie Transaktionsunterstützung über mehrere Partitionen benötigen, müssen Sie diese wahrscheinlich als Teil Ihrer Anwendungslogik implementieren, da die meisten Partitionierungssysteme keine systemeigene Unterstützung bereitstellen.

Alle Datenspeicher erfordern ein gewisses Maß an Betriebsverwaltung und Überwachung. Die Aufgaben können vom Laden der Daten, Backup und Wiederherstellen der Daten bis Neuorganisieren der Daten reichen und stellen sicher, dass das System ordnungsgemäß und effizient arbeitet.

Berücksichtigen Sie die folgenden Faktoren, die sich auf die Betriebsverwaltung auswirken:

- **Wie werden die notwendigen verwaltungsbezogenen und operativen Aufgaben implementiert, wenn die Daten partitioniert sind?** Hierzu können Aufgaben zur Sicherung und Wiederherstellung, Datenarchivierung, Systemüberwachung sowie weitere administrative Aufgaben gehören. Beispielsweise kann das Verwalten von logischer Konsistenz bei Backup- und Wiederherstellungsvorgängen schwierig sein.
- **Wie werden Daten in die verschiedenen Partitionen geladen und neue Daten aus anderen Quellen hinzugefügt?** Einige Tools und Hilfsprogramme unterstützen Datenvorgänge in Shards wie das Laden von Daten in die richtige Partition möglicherweise nicht. Dies bedeutet, dass Sie möglicherweise neue Tools und Hilfsprogramme erstellen oder abrufen müssen.
- **Wie werden die Daten in regelmäßigen Abständen archiviert und gelöscht?** Um ein übermäßiges Wachstum der Partitionen zu verhindern, müssen Daten in regelmäßigen Abständen (z. B. monatlich) archiviert und gelöscht werden. Es kann notwendig sein, die Daten zu transformieren, um einem anderen Archivschema zu entsprechen.
- **Wie werden Datenintegritätsprobleme ermittelt?** Es empfiehlt sich, einen regelmäßigen Prozess zum Ermitteln möglicher Datenintegritätsprobleme auszuführen, wie z. B. Daten in einer Partition, die auf nicht vorhandene Informationen in einer anderen Partition verweisen. Der Prozess kann entweder versuchen, diese Probleme automatisch zu beheben oder an einen Operator eine Warnung zur manuellen Behebung der Probleme ausgeben. In einer E-Commerce-Anwendung können sich beispielsweise die Bestellinformationen in einer Partition befinden, aber die Posten der einzelnen Aufträge in einer anderen. Während des Prozesses der Auftragserteilung müssen Daten zu anderen Partitionen hinzugefügt werden. Wenn dieser Prozess fehlschlägt, könnten Posten gespeichert werden, für die es keine entsprechende Bestellung gibt.

Verschiedene Datenspeicherungstechnologien bieten in der Regel ihre eigenen Funktionen zur Unterstützung der Partitionierung an. In den folgenden Abschnitten werden die Optionen zusammengefasst, die durch häufig von Azure-Anwendungen verwendeten Datenspeicher implementiert werden. Es werden außerdem Überlegungen zum Entwurf von Anwendungen beschrieben, mit denen sich diese Funktionen am besten nutzen lassen.

## Partitionierungsstrategien für Azure SQL-Datenbank

Azure SQL-Datenbank ist eine relationale Datenbank-as-a-Service, die in der Cloud ausgeführt wird. Sie basiert auf dem Microsoft SQL Server. Eine relationale Datenbank teilt die Informationen in Tabellen und jede Tabelle enthält Informationen zu Entitäten als eine Reihe von Zeilen. Jede Zeile enthält Spalten, die die Daten für die einzelnen Felder einer Entität enthalten. Im Artikel [Was ist SQL-Datenbank?] auf der Microsoft-Website finden Sie ausführliche Informationen zum Erstellen und Verwenden von SQL-Datenbanken.

## Horizontale Partitionierung mit elastischer Datenbank

Die Datenmenge, die eine einzelne SQL-­Datenbank enthalten kann, ist begrenzt. Der Durchsatz wird durch architekturbezogene Faktoren sowie die Anzahl von gleichzeitigen Verbindungen eingeschränkt, die von der Datenbank unterstützt werden. Das Feature für elastische Datenbanken der SQL-Datenbank unterstützt die horizontale Skalierung für eine SQL-Datenbank. Mithilfe einer elastischen Datenbank können Sie Ihre Daten in Shards partitionieren, die über mehrere SQL-Datenbanken verteilt sind. Sie können Shards auch hinzufügen oder entfernen, wenn die zu verarbeitende Datenmenge wächst oder schrumpft. Eine elastische Datenbank kann auch zur Verringerung von Konflikten beitragen, indem die Last auf mehrere Datenbanken verteilt wird.

> [AZURE.NOTE] Die elastische Datenbank ist ein Ersatz für das Verbundfeature der Azure SQL-Datenbank. Bestehende Installationen mit Azure SQL-Datenbankverbund können mithilfe des Federations Migration Utility (Hilfsprogramm zur Verbundmigration) zu einer elastischen Datenbank migriert werden. Alternativ können Sie Ihren eigenen Shardingmechanismus implementieren, falls sich Ihr Szenario nicht von sich aus für die elastischen Datenbankfeatures eignet.

Jedes Shard wird als SQL-Datenbank implementiert. Ein Shard kann mehrere Datasets enthalten (sogenannte _Shardlets_). Jede Datenbank verwaltet Metadaten, die die darin enthaltenen Shardlets beschreiben. Ein Shardlet kann ein einzelnes Datenelement oder eine Gruppe von Elementen sein, die den gleichen Shardlet-Schlüssel verwenden. Beim Sharding von Daten in einer mehrinstanzenfähigen Anwendung kann der Shardlet-Schlüssel beispielsweise die Mandanten-ID sein, und alle Daten für einen bestimmten Mandanten können als Teil des gleichen Shardlets gespeichert werden. Daten für andere Mandanten würden in verschiedenen Shardlets gehalten werden.

Der Programmierer ist dafür verantwortlich, einem Dataset einen Shardlet-Schlüssel zuzuordnen. Eine separate SQL-Datenbank fungiert als globaler Shardzuordnungs-Manager. Diese Datenbank enthält eine Liste aller Shards und Shardlets im System. Eine Clientanwendung, die auf Daten zugreift, stellt zuerst eine Verbindung mit der globalen Datenbank des Shardzuordnungs-Managers her, um eine Kopie der Shardzuordnung (Auflistung der Shards und Shardlets) abzurufen, und speichert diese Kopie dann lokal zwischen.

Die Anwendung nutzt dann diese Informationen, um Datenanforderungen an das entsprechenden Shard weiterzuleiten. Diese Funktionalität ist hinter einer Reihe von APIs verborgen, die in der als NuGet-Paket verfügbaren Azure SQL-Datenbank-Clientbibliothek für elastische Datenbanken enthalten sind. Die Seite [Übersicht über Features für elastische Datenbanken] auf der Microsoft-Website bietet eine ausführlichere Einführung in elastische Datenbanken.

> [AZURE.NOTE] Sie können die globale Datenbank des Shardzuordnungs-Managers replizieren, um die Latenz zu verringern und die Verfügbarkeit zu verbessern. Wenn Sie die Datenbank im Rahmen eines Premium-Tarifs implementieren, können Sie die aktive Georeplikation konfigurieren, um kontinuierlich Daten in Datenbanken in verschiedenen Regionen zu kopieren. Erstellen Sie eine Kopie der Datenbank in jeder Region, in der Benutzer vorhanden sind. Konfigurieren Sie die Anwendung anschließend so, dass sie eine Verbindung mit dieser Kopie herstellt, um die Shardzuordnung abzurufen.

> Ein alternativer Ansatz ist die Verwendung der Azure SQL-Datensynchronisierung oder einer Azure Data Factory-Pipeline, um die Shardzuordnungs-Manager-Datenbank über Regionen hinweg zu replizieren. Diese Form der Replikation wird in regelmäßigen Abständen ausgeführt und eignet sich besser, wenn die Shardzuordnung unregelmäßig geändert wird. Darüber hinaus muss die Shardzuordnungs-Manager-Datenbank nicht in einem Premium-Tarif erstellt werden.

Elastische Datenbanken bieten zwei Schemas für das Zuordnen von Daten zu Shardlets und deren Speicherung in Shards:

- Eine **listenbasierte Shardzuordnung** beschreibt die Zuordnung zwischen einem einzelnen Schlüssel und einem Shardlet. In einem mehrinstanzenfähigen System können beispielsweise die Daten für jeden Mandanten einem eindeutigen Schlüssel zugeordnet und in einem eigenen Shardlet gespeichert werden. Um Datenschutz und strikte Trennung zu gewährleisten (um zu verhindern, dass ein Mandant die Datenspeicherressourcen anderer Mandanten verbraucht), kann jedes Shardlet innerhalb eines eigenen Shards gespeichert werden.

![Verwenden einer listenbasierten Shardzuordnung zum Speichern von Mandantendaten in separaten Shards](media/best-practices-data-partitioning/PointShardlet.png)

_Abbildung 4. Verwenden einer listenbasierten Shardzuordnung zum Speichern von Mandantendaten in separaten Shards_

- Eine **bereichsbasierte Shardzuordnung** beschreibt die Zuordnung zwischen einem Satz zusammenhängender Schlüsselwerte und einem Shardlet. Im zuvor beschriebenen Beispiel einer mehrinstanzenfähigen Anwendung können Sie als Alternative zum Implementieren dedizierter Shardlets die Daten für eine Reihe von Mandanten (jeweils mit ihrem eigenen Schlüssel) innerhalb des gleichen Shardlets gruppieren. Dieses Schema ist kostengünstiger als das erste (da Mandanten Datenspeicherressourcen gemeinsam nutzen), birgt jedoch das Risiko eines geringeren Datenschutzes und einer weniger strikten Trennung.

![Verwenden einer bereichsbasierten Shardzuordnung zum Speichern von Daten für einen Reihe von Mandanten in einem Shard](media/best-practices-data-partitioning/RangeShardlet.png)

_Abbildung 5: Verwenden einer bereichsbasierten Shardzuordnung zum Speichern von Daten für einen Reihe von Mandanten in einem Shard_

Beachten Sie, dass ein einzelnes Shard die Daten für mehrere Shardlets enthalten kann. Beispielsweise können Sie listenbasierte Shardlets verwenden, um Daten für verschiedene nicht zusammenhängende Mandanten im gleichen Shard zu speichern. Sie können auch bereichs- und listenbasierte Shardlets im gleichen Shard kombinieren, obwohl der Zugriff auf die Shardlets über verschiedene Zuordnungen hinweg in der globalen Datenbank des Shardzuordnungs-Managers erfolgt. (Die globale Datenbank des Shardzuordnungs-Managers kann mehrere Shardzuordnungen enthalten.) Abbildung 6 zeigt diesen Ansatz.

![Implementieren von mehreren Shardzuordnungen](media/best-practices-data-partitioning/MultipleShardMaps.png)

_Abbildung 6: Implementieren von mehreren Shardzuordnungen_

Das Partitionierungsschema, das Sie implementieren, kann die Leistung Ihres Systems erheblich beeinflussen. Es kann sich auch darauf auswirken, wie häufig Shards hinzugefügt oder entfernt oder wie häufig Daten über Shards hinweg neu partitioniert werden müssen. Berücksichtigen Sie beim Partitionieren von Daten mithilfe einer elastischen Datenbank folgende Punkte:

- Gruppieren Sie Daten, die im gleichen Shard verwendet werden, und vermeiden Sie Vorgänge, die auf Daten in mehreren Shards zugreifen müssen. Beachten Sie, dass es sich bei einem Shard im Kontext elastischer Datenbanken um eine selbständige SQL-Datenbank handelt und dass die Azure SQL-Datenbank keine datenbankübergreifenden Verknüpfungen unterstützt (diese Vorgänge müssen auf der Clientseite ausgeführt werden). Beachten Sie außerdem, dass in Azure SQL-Datenbank Einschränkungen der referenziellen Integrität, Trigger und gespeicherte Prozeduren in einer Datenbank nicht auf Objekte in einer anderen Datenbank verweisen können. Entwerfen Sie also kein System, in dem Abhängigkeiten zwischen Shards bestehen. Eine SQL-Datenbank kann allerdings Tabellen mit Kopien von Referenzdaten enthalten, die häufig von Abfragen und anderen Vorgängen verwendet werden. Diese Tabellen müssen nicht zu einem spezifischen Shardlet gehören. Durch Replizieren dieser Daten über Shards hinweg kann die Notwendigkeit entfallen, Daten zu verknüpfen, die sich über mehrere Datenbanken erstrecken. Im Idealfall sollten diese Daten statisch oder langsam bewegend sein, um den Replikationsaufwand zu minimieren und die Wahrscheinlichkeit reduzieren, dass sie veralten.

	> [AZURE.NOTE] Obwohl eine SQL-Datenbank keine datenbankübergreifenden Verknüpfungen unterstützt, ermöglicht die API für elastische Datenbanken die Ausführung von shardübergreifenden Abfragen. Diese Abfragen können transparent die Daten in allen Shardlets durchlaufen, die in einer Shardzuordnung referenziert werden. Die API für elastische Datenbanken unterteilt shardübergreifende Abfragen in eine Reihe einzelner Abfragen (eine für jede Datenbank) und führt dann die Ergebnisse zusammen. Weitere Informationen finden Sie auf der Microsoft-Website im Artikel [Abfragen von mehreren Shards].

- Die Daten, die in Shardlets gespeichert sind, die zu der gleichen Shardzuordnung gehören, sollten das gleiche Schema besitzen. Erstellen Sie z. B. keine Listen-Shardzuordnung, die auf einige Shardlets, die Mandantendaten enthalten, oder auf andere Shardlets, die Produktinformationen enthalten, verweist. Diese Regel wird von der elastischen Datenbank zwar nicht erzwungen, die Datenverwaltung und Abfragen werden jedoch sehr komplex, wenn jedes Shardlet ein anderes Schema hat. Im eben erwähnten Beispiel wäre es eine gute Lösung, zwei listenbasierte Shardzuordnungen zu erstellen: eine zum Referenzieren der Mandantendaten und eine weitere, die auf Produktinformationen verweist. Denken Sie daran, dass die Daten, die zu unterschiedlichen Shardlets gehören, in der gleichen Shard gespeichert werden können.

	> [AZURE.NOTE] Die übergreifende Shard-Abfragefunktion der API für die elastische Datenbank hängt von jedem Shardlet in der Shardzuordnung mit dem gleichen Schema ab.

- Transaktionsvorgänge werden nur für die Daten innerhalb des gleichen Shards unterstützt, nicht über Shards hinweg. Transaktionen können sich über Shardlets erstrecken, solange sie dem gleichen Shard angehören. Wenn Ihre Geschäftslogik Transaktionen durchführen muss, sollten Sie daher die betroffenen Daten in der gleichen Shard speichern oder letztendliche Konsistenz implementieren. Weitere Informationen finden Sie unter [Data Consistency Primer] (Grundlagen der Datenkonsistenz).
- Platzieren Sie Shards in der Nähe der Benutzer, die auf die Daten in diesen Shards zugreifen (sorgen Sie also für eine geografische Standortnähe der Shards). Diese Strategie hilft dabei, Latenzen zu reduzieren.
- Vermeiden Sie eine Mischung aus sehr aktiven (Hotspots) und relativ inaktiven Shards. Versuchen Sie, die Last gleichmäßig über Shards hinweg zu verteilen. Dies erfordert möglicherweise ein Hashing der Shardletschlüssel.
- Stellen Sie bei der Geolokalisierung von Shards sicher, dass die Schlüssel mit Hash Shardlets zugeordnet sind, die in der Nähe der Benutzer gespeichert sind, die auf diese Daten zugreifen.
- Derzeit wird nur ein begrenzter Satz von SQL-Datentypen als Shardlet-Schlüssel unterstützt: _Int, Bigint, Varbinary_ und _Uniqueidentifier_. SQL _Int-_ und _Bigint-_Typen entsprechen den _Int-_ und _long-_ Datentypen in C# und haben die gleichen Bereiche. Der SQL _Varbinary-_Typ kann mit einer _Byte_ Array im C# verwendet werden und der SQL- _Uniqueidentier-_Typ entspricht der _Guid_ -Klasse in .NET Framework.

Wie der Name schon sagt, kann ein System mit elastischen Datenbanken Shards hinzufügen oder entfernen, je nachdem wie sich das Datenvolumen verkleinert oder vergrößert. Durch die APIs in der Azure SQL-Datenbank-Clientbibliothek für elastische Datenbanken kann eine Anwendung Shards dynamisch erstellen und löschen (und transparent den Shardzuordnungs-Manager aktualisieren). Das Entfernen eines Shards ist jedoch ein destruktiver Vorgang, der auch das Löschen aller Daten in diesem Shard erfordert.

Wenn eine Anwendung einen Shard in zwei separate Shards aufteilen oder Shards kombinieren muss, bietet die elastische Datenbank einen separaten Dienst zum Aufteilen/Zusammenführen. Dieser Dienst wird in einem in der Cloud gehosteten Dienst ausgeführt (der vom Entwickler erstellt werden muss) und migriert Daten sicher zwischen Shards. Weitere Informationen finden Sie auf der Microsoft-Website unter [Skalierung mit dem Split-Merge-Tool für elastische Datenbanken].

## Partitionierungsstrategien für Azure-Speicher

Azure-Speicher bietet drei Abstraktionen für die Verwaltung von Daten:

- Tabellenspeicher, der skalierbaren Strukturspeicher implementiert. Eine Tabelle enthält eine Sammlung von Entitäten, von denen jede einen Satz von Eigenschaften und Werten enthalten kann.
- Blobspeicher, der Speicher für große Objekte und Dateien bereitstellt.
- Speicherwarteschlangen, die zuverlässiges asynchrones Messaging zwischen Anwendungen unterstützen.

Tabellenspeicher und Blobspeicher sind im Wesentlichen Schlüssel-Wert-Speicher, die für strukturierte bzw. unstrukturierte Daten optimiert sind. Speicherwarteschlangen bieten einen Mechanismus zum Erstellen von lose gekoppelten, skalierbaren Anwendungen. Tabellenspeicher, Blobspeicher und Speicherwarteschlangen werden im Kontext eines Azure Storage-Kontos erstellt. Speicherkonten unterstützen drei Arten von Redundanz:

- **Lokal redundanter Speicher**, der drei Kopien von Daten in einem einzigen Rechenzentrum verwaltet. Diese Form der Redundanz schützt vor Hardwarefehlern, aber nicht gegen einen Notfall, der das gesamte Datencenter umfasst.
- **Zonenredundanter Speicher**, der drei Kopien von Daten verwaltet, die auf verschiedene Rechenzentren innerhalb derselben Region (oder in zwei geografisch nahen Regionen) verteilt sind. Diese Form der Redundanz schützt vor Katastrophen, die innerhalb eines einzigen Datacenters auftreten können, aber nicht vor umfangreichen Netzwerktrennungen, die eine ganze Region betreffen. Beachten Sie, dass zonenredundanter Speicher aktuell nur für Blockblobs verfügbar ist.
- **Georedundanter Speicher**, der sechs Kopien von Daten verwaltet: drei Kopien in einer Region (Ihrer lokalen Region) und weitere drei Kopien in einer entfernten Region. Diese Form der Redundanz bietet den höchsten Schutz für Notfälle.

Microsoft hat Skalierbarkeitsziele für Azure Storage veröffentlicht. Weitere Informationen finden Sie auf der Microsoft-Website im Artikel [Skalierbarkeits- und Leistungsziele für Azure Storage]. Derzeit darf die Gesamtspeicherkapazität eines Kontos 500 TB nicht überschreiten. (Dazu gehören auch die Daten in Tabellen- und Blobspeichern sowie ausstehende Nachrichten in Speicherwarteschlangen.)

Die maximale Anforderungsrate (bei einer angenommenen Entitäts-, Blob- oder Nachrichtengröße von 1 KB) beträgt 20 KB/s. Wenn diese Grenzwerte in Ihrem System voraussichtlich überschritten werden, sollten Sie die Last über mehrere Speicherkonten hinweg partitionieren. In einem einzelnen Azure-Abonnement können bis zu 100 Speicherkonten erstellt werden. Beachten Sie jedoch, dass sich diese Grenzwerte im Laufe der Zeit ändern können.

## Partitionierung des Microsoft Azure-Tabellenspeichers

Azure-Tabellenspeicher ist ein Schlüssel-Wert-Speicher, der mit Fokus auf die Partitionierung entworfen wurde. Alle Entitäten werden in einer Partition gespeichert und Partitionen werden intern von Azure-Tabellenspeicher verwaltet. Jede in einer Tabelle gespeicherte Entität muss einen zweiteiligen Schlüssel bereitstellen, der Folgendes umfasst:

- **Partitionsschlüssel.** Dies ist ein Zeichenfolgenwert, der bestimmt, in welcher Partition der Azure-Tabellenspeicher die Entität platzieren wird. Alle Entitäten mit demselben Partitionsschlüssel werden in der gleichen Partition gespeichert werden.
- **Zeilenschlüssel.** Dies ist ein anderer String-Zeichenfolgewert, der die Entität innerhalb der Partition identifiziert. Alle Entitäten innerhalb einer Partition werden durch diesen Schlüssel lexikalisch, in aufsteigender Reihenfolge sortiert. Die Kombination aus Partitions- und Zeilenschlüssel muss für jede Entität eindeutig sein und darf 1 KB Länge nicht überschreiten.

Der Rest der Daten für eine Entität besteht aus anwendungsdefinierten Feldern. Es werden keine bestimmte Schemen erzwungen und jede Zeile kann einen anderen Satz von anwendungsdefinierten Feldern enthalten. Die einzige Einschränkung besteht darin, dass die maximale Größe einer Entität (einschließlich des Partitions- und des Zeilenschlüssels) derzeit 1 MB beträgt. Die maximale Größe einer Tabelle beträgt 200 TB, diese Zahlen können sich jedoch in Zukunft ändern. (Aktuelle Informationen zu diesen Grenzwerten finden Sie auf der Microsoft-Website im Artikel [Skalierbarkeits- und Leistungsziele für Azure Storage].)

Wenn Sie Entitäten speichern müssen, die diese Kapazität überschreiten, sollten Sie diese auf mehrere Tabelle aufteilen. Verwenden Sie die vertikale Partitionierung, um die Felder in die Gruppen zu teilen, auf die höchstwahrscheinlich gemeinsam zugegriffen wird.

Abbildung 7 zeigt die logische Struktur eines Beispielspeicherkontos (Contoso-Daten) für eine fiktive E-Commerce-Anwendung. Das Speicherkonto enthält drei Tabellen mit Informationen zu Kunden, Produkten und Bestellungen. Jede Tabelle verfügt über mehrere Partitionen.

In der Tabelle mit den Kundeninformationen sind die Daten nach der Stadt partitioniert, in der sich der Kunde befindet, und der Zeilenschlüssel enthält die Kunden-ID. In der Tabelle mit den Produktinformationen sind die Produkte nach Kategorie partitioniert, und der Zeilenschlüssel enthält die Produktnummer. In der Tabelle mit den Bestellinformationen sind die Bestellungen nach dem Datum partitioniert, an dem sie aufgegeben wurden, und der Zeilenschlüssel gibt die Uhrzeit an, zu der die Bestellung eingegangen ist. Beachten Sie, dass alle Daten anhand der Zeilenschlüssel in jeder Partition sortiert sind.

![Die Tabellen und Partitionen in einem Beispielspeicherkonto](media/best-practices-data-partitioning/TableStorage.png)

_Abbildung 7: Die Tabellen und Partitionen in einem Beispielspeicherkonto_

> [AZURE.NOTE] Azure-Tabellenspeicher fügt auch ein Timestamp-Feld für jede Entität hinzu. Das Timestamp-Feld wird vom Tabellenspeicher verwaltet und wird jedes Mal aktualisiert, wenn die Entität geändert und erneut in eine Partition geschrieben wird. Der Tabellenspeicherdienst verwendet dieses Feld zum Implementieren einer optimistischen Nebenläufigkeit. Jedes Mal, wenn eine Anwendung eine Entität zurück in den Tabellenspeicher schreibt, vergleicht der Tabellenspeicherdienst den Wert des Zeitstempels in der geschriebenen Entität mit dem im Tabellenspeicher vorhandenen Wert. Wenn sich die Werte unterscheiden, bedeutet dies, dass eine andere Anwendung die Entität seit dem letzten Abruf geändert hat, und beim Schreibvorgang tritt ein Fehler auf. Ändern Sie dieses Feld in Ihrem eigenen Code nicht, und legen Sie auch keinen Wert für dieses Feld fest, wenn Sie eine neue Identität erstellen.

Der Azure-Tabellenspeicher verwendet den Partitionsschlüssel, um zu bestimmen, wie die Daten zu speichern sind. Wenn eine Entität einer Tabelle mit einem zuvor nicht verwendetem Partitionsschlüssel hinzugefügt wird, erstellt der Azure-Tabellenspeicher eine neue Partition für diese Entität. Andere Entitäten mit demselben Partitionsschlüssel werden in der gleichen Partition gespeichert.

Dieser Mechanismus implementiert effektiv eine Strategie für die automatische Skalierungsstrategie. Jede Partition wird auf einem einzigen Server in einem Azure-Rechenzentrum gespeichert, um sicherzustellen, dass Abfragen, die Daten aus einer einzigen Partition abrufen, schnell ausgeführt werden. Verschiedene Partitionen können jedoch auf mehrere Server verteilt werden. Darüber hinaus kann ein einzelner Server mehrere Partitionen hosten, wenn diese Partitionen in ihrer Größe beschränkt sind.

Berücksichtigen Sie die folgenden Punkte beim Entwerfen der Entitäten für den Azure-Tabellenspeicher:

- Die Auswahl der Partitions- und Zeilenschlüssel sollten auf die Weise gesteuert werden, in der auf die Daten zugegriffen wird. Wählen Sie eine Kombination aus Partitions- und Zeilenschlüssel, die den Großteil Ihrer Abfragen unterstützt. Die effizientesten Abfragen rufen Daten durch Angeben des Partitions- und des Zeilenschlüssels ab. Abfragen, die einen Partitionsschlüssel und einen Zeilenschlüsselbereich angeben, können durch Durchsuchen einer einzigen Partition ausgeführt werden. Dies ist ein relativ schneller Vorgang, da die Daten in der Reihenfolge der Zeilenschlüssel gespeichert sind. Wenn die zu durchsuchende Partition in einer Abfrage nicht angegeben ist, erfordert der Partitionsschlüssel möglicherweise, dass der Azure-Tabellenspeicher jede Partition nach den Daten durchsucht.

	> [AZURE.TIP] Wenn eine Entität einen natürlichen Schlüssel hat, dann verwenden Sie diesen als Partitionsschlüssel und geben Sie eine leere Zeichenfolge als Zeilenschlüssel an. Wenn eine Entität über einen zusammengesetzten Schlüssel aus zwei Eigenschaften verfügt, wählen Sie die Eigenschaft, die sich am langsamsten ändert, als Partitionsschlüssel und die andere als Zeilenschlüssel. Wenn eine Entität über mehr als zwei Schlüsseleigenschaften verfügt, verwenden Sie eine Verkettung der Eigenschaften, um die Partitions- und Zeilenschlüssel bereitzustellen.

- Wenn Sie regelmäßig Abfragen durchführen, die Daten mithilfe anderer Felder als den Partitions- und Zeilenschlüsseln aufrufen, sollten Sie ein [Index Table Pattern] (Indextabellenmuster) implementieren.
- Wenn Sie Partitionsschlüssel mithilfe einer monoton ansteigenden oder absteigenden Sequenz generieren (z. B. „0001“, „0002“, „0003“ usw.) und jede Partition nur eine begrenzte Menge von Daten enthält, dann kann der Azure-Tabellenspeicher diese Partitionen physisch auf demselben Server gruppieren. Dieser Mechanismus geht davon aus, dass die Anwendung am ehesten Abfragen über einen zusammenhängenden Bereich von Partitionen (Bereichsabfragen) durchführen wird und ist für diesen Fall optimiert. Dieser Ansatz kann jedoch zu Hotspots mit Fokus auf einen einzelnen Server führen, da es wahrscheinlich ist, dass alle neu eingefügten Entitäten an dem einen oder dem anderen Ende der zusammenhängenden Bereiche konzentriert werden. Er kann auch die Skalierbarkeit verringern. Um die Last gleichmäßiger auf Server zu verteilen, sollten Sie ein Hashing des Partitionsschlüssels erwägen, um die Sequenz zufälliger zu gestalten.
- Azure-Tabellenspeicher unterstützt Transaktionsvorgänge für Entitäten, die zur selben Partition gehören. Dies bedeutet, dass eine Anwendung mehrere Einfüge-, Aktualisierungs-, Lösch-, Ersetzungs- oder Zusammenführungsvorgänge in Form einer atomischen Einheit ausführen kann (sofern die Transaktion nicht mehr als 100 Entitäten umfasst und die Nutzlast der Anforderung 4 MB nicht überschreitet). Vorgänge, die sich über mehrere Partitionen erstrecken, sind nicht transaktional und erfordern möglicherweise die Implementierung von letztlicher Konsistenz, wie unter [Data Consistency Primer] (Grundlagen der Datenkonsistenz) beschrieben. Weitere Informationen zu Tabellenspeichern und Transaktionen finden Sie auf der Microsoft-Website im Artikel [Ausführen von Entitätsgruppentransaktionen].
- Achten Sie aus folgenden Gründen besonders auf die Granularität der Partitionsschlüssel:
	- Die Verwendung des gleichen Partitionsschlüssels für jede Entität führt dazu, dass der Tabellenspeicherdienst eine einzelne große Partition erstellt, die auf einem Server gespeichert wird. Dies verhindert ein horizontales Hochskalieren und konzentriert die Last auf einen einzigen Server. Daher eignet sich diese Vorgehensweise nur für Systeme, die eine kleine Anzahl von Entitäten verwalten. Dieser Ansatz gewährleistet jedoch, dass alle Entitäten in die Entitätsgruppentransaktionen einbezogen werden können.
	- Die Verwendung eines eindeutigen Partitionsschlüssels für jede Entität führt dazu, dass der Tabellenspeicherdienst eine separate Partition für jede Entität erstellt, was (je nach Größe der Entitäten) möglicherweise zu einer großen Anzahl von kleinen Partitionen führen kann. Dieser Ansatz bietet mehr Skalierbarkeit als ein einzelner Partitionsschlüssel, Gruppentransaktionen für Entitäten sind jedoch nicht möglich. Auch erfordern Abfragen, die mehr als eine Entität abrufen, möglicherweise Lesevorgänge auf mehr als einem Server. Wenn die Anwendung jedoch Bereichsabfragen ausführt, kann die Verwendung einer monotonen Sequenz zum Generieren der Partitionsschlüssel zur Optimierung dieser Abfragen beitragen.
	- Durch Nutzung eines einzelnen Partitionsschlüssels für eine Teilmenge von Entitäten können Sie verknüpfte Entitäten in derselben Partition gruppieren. Vorgänge mit verknüpften Entitäten lassen sich mithilfe von Gruppentransaktionen für Entitäten durchführen, und Abfragen, die einen Satz verknüpfter Entitäten abrufen, können durch Zugriff auf einen einzigen Server durchgeführt werden.

Weitere Informationen zum Partitionieren von Daten im Azure-Tabellenspeicher finden Sie auf der Microsoft-Website im Artikel [Azure-Speichertabelle – Entwurfshandbuch].

## Partitionierung des Azure-Blobspeichers

Azure-Blobspeicher ermöglicht die Speicherung großer Binärobjekte – zurzeit bis zu 200 GB für Blockblobs oder 1 TB für Seitenblobs. (Aktuelle Informationen finden Sie auf der Microsoft-Website im Artikel [Skalierbarkeits- und Leistungsziele für Azure Storage].) Verwenden Sie die Block-Blobs in Szenarien wie z. B. Streaming, in denen Sie große Datenmengen schnell hoch- oder herunterladen müssen. Verwenden Sie Seitenblobs für Anwendungen, die eher zufälligen als seriellen Zugriff auf Teile der Daten erfordern.

Jedes Blob (Block oder Seite), wird in einem Container in einem Azure-Speicherkonten aufrechterhalten. Sie können Container verwenden, um verwandte Blobs zu gruppieren, die die gleichen Sicherheitsanforderungen aufweisen, obwohl diese Gruppierung eher logisch statt physisch ist. In einem Container hat jedes Blob einen eindeutigen Namen.

Der Blob-Speicher wird automatisch basierend auf dem Blob-Namen partitioniert. Jedes Blob ist in einer eigenen Partition gespeichert. Blobs im gleichen Container nutzen nicht die gleiche Partition. Dank dieser Architektur kann der Azure-Blobspeicher einen serverübergreifenden transparenten Lastenausgleich durchführen, da unterschiedliche Blobs im gleichen Container auf verschiedene Server verteilt werden können.

Die Aktionen des Schreibens eines einzelnen Blocks (Block-Blob) oder einer Seite (Seitenblob) sind atomar, aber Vorgänge, die Blöcke, Seiten oder Blobs umfassen, hingegen nicht. Wenn Sie bei der Ausführung von Schreibvorgängen Konsistenz zwischen Blöcken, Seiten und Blobs gewährleisten müssen, richten Sie mithilfe einer Bloblease eine Schreibsperre ein.

Azure-Blobspeicher unterstützt Übertragungsraten von bis zu 60 MB pro Sekunde oder 500 Anforderungen pro Sekunde für jedes Blob. Wenn Sie erwarten, diese Grenzwerte zu überschreiten und die Blobdaten relativ statisch sind, sollten Sie die Replikation von Blobs mithilfe von Azure Content Delivery Network erwägen. Weitere Informationen finden Sie auf der Microsoft-Website unter [Verwenden von Content Delivery Network für Azure]. Weitere Anleitungen und Überlegungen finden Sie im Artikel [Verwenden von CDN für Azure].

## Partitionierung von Azure-Speicherwarteschlangen

Azure-Speicherwarteschlangen ermöglichen Ihnen asynchrones Messaging zwischen Prozessen. Ein Azure-Speicherkonto kann eine beliebige Anzahl an Warteschlangen enthalten und jede Warteschlange kann eine beliebige Anzahl an Nachrichten enthalten. Die einzige Beschränkung ist der im Speicherkonto verfügbare Platz. Die maximale Größe einer einzelnen Nachricht beträgt 64 KB. Falls Sie größere Nachrichten erfordern, dann sollten Sie in Betracht ziehen, stattdessen Azure Service Bus-Warteschlangen zu verwenden.

Jede Speicherwarteschlange verfügt über einen eindeutigen Namen innerhalb des Speicherkontos, in dem sie enthalten ist. Azure partitioniert Warteschlangen basierend auf dem Namen. Alle Nachrichten für dieselbe Warteschlange werden in derselben Partition gespeichert, die von einem einzigen Server kontrolliert wird. Verschiedene Warteschlangen können von verschiedenen Servern verwaltet werden, um beim Lastausgleich zu helfen. Die Zuordnung der Warteschlangen zu Servern ist für Anwendungen und Benutzer transparent.

 Verwenden Sie in einer umfangreichen Anwendung nicht die gleiche Speicherwarteschlange für alle Instanzen der Anwendung, da bei diesen Ansatz der Server, der die Warteschlange hostet, zum Hotspot werden kann. Verwenden Sie stattdessen verschiedene Warteschlangen für verschiedene Funktionsbereiche der Anwendung. Azure-Speicherwarteschlangen unterstützen keine Transaktionen, d.h. das Weiterleiten von Nachrichten an verschiedene Warteschlangen sollte nur eine geringe Auswirkung auf die Messaging-Konsistenz haben.

Eine Azure-Speicherwarteschlange kann bis zu 2.000 Nachrichten pro Sekunde verarbeiten. Wenn Sie Nachrichten schneller verarbeiten müssen, sollten Sie das Erstellen mehrerer Warteschlangen in Betracht ziehen. Erstellen Sie z. B. in einer globalen Anwendung separate Speicherwarteschlangen in separaten Speicherkonten, um Anwendungsinstanzen zu verarbeiten, die in jeder Region ausgeführt werden.

## Partitionierungsstrategien für Azure-Service Bus

Azure Service Bus verwendet einen Nachrichtenbroker, um Nachrichten zu verarbeiten, die an eine Service Bus-Warteschlange oder ein Topic gesendet wurden. Standardmäßig werden alle an eine Warteschlange oder ein Topic gesendeten Nachrichten vom gleichen Nachrichtenbrokerprozess verarbeitet. Diese Architektur kann den Gesamtdurchsatz der Nachrichten-Warteschlange eingrenzen. Sie können eine Warteschlange oder ein Topic jedoch auch während der Erstellung partitionieren. Legen Sie dazu die _EnablePartitioning_-Eigenschaft der Warteschlange oder des Topics auf _true_ fest.

Eine partitionierte Warteschlange oder ein partitioniertes Topic ist in mehrere Fragmente unterteilt, von denen jedes durch einen separaten Nachrichtenspeicher und Nachrichtenbroker gesichert ist. Service Bus übernimmt die Verantwortung zum Erstellen und Verwalten dieser Fragmente. Wenn eine Anwendung eine Nachricht an eine partitionierte Warteschlange oder ein partitioniertes Thema postet, ordnet Service Bus die Nachricht einem Fragment für diese Warteschlange oder dieses Thema zu. Wenn eine Anwendung eine Nachricht von einer Warteschlange oder einem Abonnement empfängt, überprüft Service Bus jedes Fragment auf die nächste verfügbare Nachricht und leitet diese dann an die Anwendung zur Verarbeitung weiter.

Mit dieser Struktur kann die Last auf die Nachrichtenbroker und Nachrichtenspeicher verteilt werden, um die Skalierbarkeit und Verfügbarkeit zu verbessern. Wenn der Nachrichtenbroker oder der Nachrichtenspeicher für ein Fragment vorübergehend nicht verfügbar ist, kann Service Bus Nachrichten aus einem der anderen verfügbaren Fragmente abrufen.

Service Bus ordnet jeder Nachricht folgendermaßen ein Fragment zu:

- Wenn die Nachricht zu einer Sitzung gehört, werden alle Nachrichten mit dem gleichen Wert für die _SessionId_-Eigenschaft an dasselbe Fragment gesendet.
- Wenn die Nachricht nicht zu einer Sitzung gehört, aber der Absender einen Wert für die _PartitionKey_-Eigenschaft angegeben hat, werden alle Nachrichten mit demselben _PartitionKey_-Wert an dasselbe Fragment gesendet.

	> [AZURE.NOTE] Wenn die Eigenschaften _SessionId_ und _PartitionKey_ angegeben werden, müssen sie auf denselben Wert festgelegt werden, andernfalls wird die Nachricht abgelehnt.
- Wenn die Eigenschaften _SessionID_ und _PartitionKey_ für eine Nachricht nicht angegeben sind, aber Duplikaterkennung aktiviert ist, wird die Eigenschaft _MessageID_ verwendet. Alle Nachrichten mit derselben _MessageID_ werden an dasselbe Fragment geleitet.
- Wenn Nachrichten keine _SessionId-, PartitionKey_- oder _MessageId_-Eigenschaft enthalten, ordnet Service Bus die Nachrichten den Fragmenten sequenziell zu. Wenn ein Fragment nicht verfügbar ist, geht Service Bus zum nächsten über. Dies bedeutet, dass ein vorübergehender Fehler in der Nachrichteninfrastruktur nicht dazu führt, dass der Nachrichtensendevorgang nicht ausgeführt werden kann.

Beachten Sie die folgende Punkte, wenn Sie entscheiden, ob und wie Sie eine Service Bus-Nachrichtenwarteschlange oder ein Service Bus-Topic partitionieren:

- Service Bus-Warteschlangen und -Themen werden im Umfang des Service Bus-Namespace erstellt. Service Bus erlaubt derzeit bis zu 100 partitionierte Warteschlangen oder Themen pro Namespace.
- Jeder Service Bus-Namespace setzt Kontingente für die verfügbaren Ressourcen fest, wie z. B. die Anzahl der Abonnements pro Topic, die Anzahl gleichzeitiger Sende- und Empfangsanforderungen und die maximale Anzahl gleichzeitiger Verbindungen, die aufgebaut werden können. Diese Kontingente sind auf der Microsoft-Website unter [Service Bus-Kontingente] dokumentiert. Wenn Sie diese Werte überschreiten möchten, dann erstellen Sie zusätzliche Namespaces mit ihren eigenen Warteschlangen und Themen und verteilen Sie die Arbeit auf diese Namespaces. Erstellen Sie z. B. in einer globalen Anwendung separate Namespaces in jeder Region und konfigurieren Sie die Anwendungsinstanzen so, dass sie die Warteschlangen und Themen in nächstliegendem Namespace verwenden.
- Nachrichten, die als Teil einer Transaktion gesendet werden, müssen einen Partitionsschlüssel angeben. Dies kann eine _SessionId_-, _PartitionKey_- oder _MessageId_-Eigenschaft sein. Alle Nachrichten, die als Teil derselben Transaktion gesendet werden, müssen denselben Partitionsschlüssel angeben, da sie vom selben Nachrichtenbroker-Prozess verarbeitet werden müssen. Sie können keine Nachrichten an verschiedene Warteschlangen oder Themen innerhalb der gleichen Transaktion senden.
- Partitionierte Warteschlangen und Topics können nicht so konfiguriert werden, dass sie im Leerlauf automatisch gelöscht werden.
- Partitionierte Warteschlangen und Topics können zurzeit nicht mit dem Advanced Message Queuing Protocol (AMQP) verwendet werden, wenn Sie plattformübergreifende oder hybride Lösungen erstellen.

## Partitionierungsstrategien für Azure DocumentDB-Datenbanken

Azure DocumentDB ist eine NoSQL-Datenbank, die Dokumente speichern kann. Ein Dokument in einer DocumentDB-Datenbank ist eine JSON-serialisierte Darstellung eines Objekts oder sonstigen Datensegments. Es werden keine festgelegten Schemen erzwungen, außer dass jedes Dokument über eine eindeutige ID verfügen muss.

Dokumente werden in Auflistungen organisiert. In einer Sammlung können Sie verwandte Dokumente gruppieren. Beispielsweise könnten Sie in einem System, das Blogbeiträge verwaltet, den Inhalt jedes Blogbeitrags als Dokument in einer Sammlung speichern. Sie können auch Sammlungen für jeden Thementyp erstellen. Alternativ dazu könnten Sie in einer mehrinstanzenfähigen Anwendung, wie beispielsweise einem System, in dem verschiedene Autoren ihre eigenen Blogbeiträge kontrollieren und verwalten können, Blogs nach Autor partitionieren und für jeden Autor eine separate Sammlung erstellen. Der Speicherplatz, der Sammlungen zugeordnet ist, ist elastisch und kann je nach Bedarf schrumpfen oder wachsen.

Dokumentsammlungen bieten einen natürlichen Mechanismus zum Partitionieren von Daten innerhalb einer Einzeldatenbank. Intern kann eine DocumentDB-Datenbank mehrere Server umspannen und versucht möglicherweise, die Last zu verteilen, indem Sammlungen auf die Server aufgeteilt werden. Der einfachste Weg Sharding zu implementieren, ist für jedes Shard eine Auflistung zu erstellen.

> [AZURE.NOTE] Jede DocumentDB-Datenbank weist eine bestimmte _Leistungsebene_ auf, die den Umfang der Ressourcen festlegt, die von der Datenbank genutzt werden können. Jeder Leistungsstufe ist eine Ratenbegrenzung für _Anforderungseinheiten_ (Request Unit, RU) zugeordnet. Die RU-Ratenbegrenzung gibt die Menge der Ressourcen an, die für diese Sammlung reserviert sind und für die ausschließliche Verwendung durch diese Sammlung zur Verfügung stehen. Die Kosten einer Sammlung richten sich nach der Leistungsstufe, die für diese Sammlung gewählt wurde. Je höher die Leistungsstufe (und die RU-Ratenbegrenzung), desto höher die Kosten. Sie können die Leistungsstufe einer Sammlung über das Azure-Portal anpassen. Weitere Informationen finden Sie unter [Leistungsebenen in DocumentDB] auf der Microsoft-Website.

Alle Datenbanken werden im Kontext eines DocumentDB-Kontos erstellt. Ein einzelnes DocumentDB-Konto kann mehrere Datenbanken enthalten und gibt an, in welcher Region die Datenbanken erstellt werden. Jedes DocumentDB-Konto erzwingt auch seine eigene Zugriffskontrolle. Sie können DocumentDB-Konten verwenden, um Shards (Sammlungen in Datenbanken) geografisch in der Nähe der Benutzer zu platzieren, die darauf zugreifen müssen, und Einschränkungen erzwingen, sodass sich nur diese Benutzer mit ihnen verbinden können.

Jedes DocumentDB-Konto verfügt über ein Kontingent, das die Anzahl von Datenbanken und Sammlungen, die es enthalten kann, sowie die Menge des verfügbaren Dokumentspeicherplatzes begrenzt. Die Einschränkungen unterliegen Veränderungen, aber werden unter [DocumentDB-Einschränkungen und Kontingente] auf der Microsoft-Website beschrieben. Theoretisch ist es möglich, dass bei der Implementierung eines Systems, in dem alle Shards zur gleichen Datenbank gehören, die Speicherkapazitätsgrenze des Kontos erreicht wird.

In diesem Fall müssen Sie möglicherweise zusätzliche DocumentDB-Konten und -Datenbanken einrichten und die Shards auf diese Datenbanken verteilen. Auch wenn es unwahrscheinlich ist, dass die maximale Speicherkapazität einer Datenbank erreicht wird, empfiehlt es sich, mehrere Datenbanken zu verwenden. Da jede Datenbank über einen eigenen Satz an Benutzern und Berechtigungen verfügt, können Sie diesen Mechanismus verwenden, um den Zugriff auf Sammlungen basierend auf der Datenbank zu steuern.

Abbildung 8 zeigt die Struktur auf oberster Ebene der DocumentDB-Architektur

![Die Struktur von DocumentDB](media/best-practices-data-partitioning/DocumentDBStructure.png)

_Abbildung 8: Struktur der DocumentDB-Architektur_

Es ist Aufgabe der Clientanwendung, Anforderungen an den geeigneten Shard zu leiten, für gewöhnlich durch Implementieren ihres eigenen Zuordnungsmechanismus, basierend auf einigen Attributen der Daten, die den Shardschlüssel definieren. Abbildung 9 zeigt zwei DocumentDB-Datenbanken, von denen jede zwei Sammlungen enthält, die als Shards agieren. Das Sharding der Daten erfolgt über eine Mandanten-ID, und die Daten sind spezifisch für einen bestimmten Mandanten. Die Datenbanken werden in separaten DocumentDB-Konten erstellt. Diese Konten befinden sich in der gleichen Region wie die Mandanten, deren Daten sie enthalten. Die Routinglogik in der Clientanwendung verwendet die Mandanten-ID als Shardschlüssel.

![Implementierung von Sharding mithilfe von Azure DocumentDB](media/best-practices-data-partitioning/DocumentDBPartitions.png)

_Abbildung 9: Implementierung von Sharding mithilfe einer Azure-DocumentDB-Datenbank_

Berücksichtigen Sie folgende Punkte bei der Entscheidung, wie Daten mit einer DocumentDB-Datenbank partitioniert werden sollen:

- **Die für eine DocumentDB-Datenbank verfügbaren Ressourcen unterliegen den Kontingentgrenzen für das DocumentDB-Konto.** Jede Datenbank kann eine Reihe von Sammlungen enthalten (mit erneuter Beschränkung) und jeder Sammlung ist eine Leistungsebene zugeordnet, die die RU-Ratenbegrenzung (reservierter Durchsatz) für diese Sammlung regelt. Weitere Informationen finden Sie auf der Microsoft-Website im Artikel [DocumentDB-Grenzen und -Kontingente].
- **Jedes Dokument muss über ein Attribut verfügen, das verwendet werden kann, um das Dokument in der Sammlung, in der es enthalten ist, eindeutig zu identifizieren.** Dieses Attribut unterscheidet sich vom Shardschlüssel, der die Sammlung definiert, in der das Dokument enthalten ist. Eine Sammlung kann eine große Anzahl von Dokumenten enthalten. Theoretisch besteht die einzige Einschränkung in der maximalen Länge der Dokument-ID. Die Dokument-ID kann bis zu 255 Zeichen lang sein.
- **Alle Vorgänge für ein Dokument werden im Kontext einer Transaktion ausgeführt. Transaktionen in DocumentDB-Datenbanken sind auf die Sammlung begrenzt, in der das Dokument enthalten ist.** Wenn ein Vorgang fehlschlägt, wird die Arbeit, die durch ihn ausgeführt wurde, zurückgesetzt. Während ein Vorgang für ein Dokument ausgeführt wird, unterliegen alle vorgenommenen Änderungen einer Isolierung auf Snapshotebene. Wenn z. B. eine Anforderung zum Erstellen eines neuen Dokuments fehlschlägt, stellt dieser Mechanismus sicher, dass einem anderen Benutzer, der die Datenbank gleichzeitig abfragt, kein Teildokument angezeigt wird, das dann entfernt wird.
- **DocumentDB-Datenbankabfragen sind auch auf die Sammlungsebene begrenzt.** Eine einzelne Abfrage kann nur Daten aus einer Sammlung abrufen. Wenn Sie Daten aus mehreren Sammlungen abrufen müssen, müssen Sie jede Sammlung einzeln abfragen und die Ergebnisse in Ihrem Anwendungscode zusammenführen.
- **DocumentDB-Datenbanken unterstützen programmierbare Elemente, die alle zusammen mit Dokumenten in einer Sammlung gespeichert werden können**. Hierzu gehören gespeicherte Prozeduren, benutzerdefinierte Funktionen und Trigger (geschrieben in JavaScript). Diese Elemente können auf alle Dokumente in der gleichen Auflistung zugreifen. Außerdem werden diese Elemente entweder im Rahmen der Ambient-Transaktion ausgeführt (im Fall eines Triggers, der als Ergebnis eines für ein Dokument ausgeführten Erstellungs-, Lösch- oder Ersetzungsvorgangs ausgelöst wird) oder durch Starten einer neuen Transaktion (im Fall einer gespeicherten Prozedur, die als Ergebnis einer expliziten Clientanforderung ausgeführt wird). Wenn der Code in einem programmierbaren Element eine Ausnahme auslöst, wird für die Transaktion ein Rollback ausgeführt. Sie können gespeicherte Prozeduren und Trigger verwenden, um die Integrität und Konsistenz zwischen den Dokumenten zu verwalten, aber diese Dokumente müssen alle derselben Auflistung angehören.
- **Die Sammlungen, die Sie in den Datenbanken in einem DocumentDB-Konto speichern möchten, sollten die von den Leistungsstufen der Sammlungen definierten Durchsatzgrenzen nicht überschreiten.** Diese Grenzwerte werden auf der Microsoft-Website im Artikel [Weitere Informationen zu Kapazität und Dokumentspeicherung in DocumentDB] beschrieben. Wenn Sie davon ausgehen, diese Grenzwerte zu erreichen, sollten Sie in Betracht ziehen, die Auflistungen auf Datenbanken in verschiedenen DocumentDB-Konten zu verteilen, um die Last pro Auflistung zu verringern.

## Partitionierungsstrategien für Azure Search

Die Fähigkeit, nach Daten zu suchen, ist häufig die primäre Navigations- und Untersuchungsmethode, die von vielen Webanwendungen bereitgestellt wird. Damit können Benutzer Ressourcen anhand einer Kombination verschiedener Suchkriterien schnell finden (beispielsweise Produkte in einer E-Commerce-Anwendung). Azure Search bietet Volltext-Suchfunktionen über Web-Inhalte und umfasst Funktionen wie z. B. Type-ahead, vorgeschlagene Abfragen basierend auf Übereinstimmungen und Facettennavigation. Eine vollständige Beschreibung dieser Funktionen finden Sie auf der Microsoft-Website im Artikel [Was ist Azure Search?].

Azure Search speichert durchsuchbare Inhalte in Form von JSON-Dokumenten in einer Datenbank. Sie definieren Indizes, um die durchsuchbaren Felder in diesen Dokumenten anzugeben, und stellen diese Definitionen Azure Search zur Verfügung. Wenn ein Benutzer eine Suchanforderung sendet, verwendet Azure Search die entsprechenden Indizes, um übereinstimmende Elemente zu finden.

Um Konflikte zu reduzieren, kann der von Azure Search verwendete Speicher in 1, 2, 3, 4, 6 oder 12 Partitionen unterteilt werden, von denen jede bis zu 6-mal repliziert werden kann. Das Produkt der Multiplikation aus Anzahl von Partitionen und Anzahl von Replikaten wird _Sucheinheit_ (Search Unit, SU) genannt. Eine einzelne Instanz von Azure Search kann maximal 36 SUs enthalten (eine Datenbank mit 12 Partitionen unterstützt nur maximal 3 Replikate).

Jede SU, die Ihrem Dienst zugeordnet wird, wird Ihnen berechnet. Wenn die Menge an durchsuchbaren Inhalten oder die Rate der Suchanforderungen wächst, können Sie einer vorhandenen Azure Search-Instanz SUs hinzufügen, um die zusätzliche Last zu verarbeiten. Azure Search selbst verteilt die Dokumente gleichmäßig über die Partitionen. Zurzeit werden keine manuellen Partitionierungsstrategien unterstützt.

Jede Partition kann maximal 15 Millionen Dokumente enthalten oder 300 GB Speicherplatz belegen (je nachdem, welche Menge geringer ist). Sie können bis zu 50 Indizes generieren. Die Leistung des Diensts variiert und ist abhängig von der Komplexität der Dokumente, den verfügbaren Indizes und den Auswirkungen von Netzwerklatenz. Im Durchschnitt sollte ein einzelnes Replikat (1 SU) 15 Abfragen pro Sekunde (Queries Per Second, QPS) verarbeiten können. Es empfiehlt sich allerdings, ein Benchmarking mit Ihren eigenen Daten durchzuführen, um ein genaueres Maß für den Durchsatz zu erhalten. Weitere Informationen finden Sie auf der Microsoft-Website im Artikel [Grenzwerte für den Azure Search-Dienst].

> [AZURE.NOTE] Sie können eine begrenzte Anzahl von Datentypen in durchsuchbaren Dokumenten speichern, z. B. Zeichenfolgen, boolesche Werte, numerische Daten, Datums-/Uhrzeitdaten und einige geografische Daten. Weitere Informationen finden Sie auf der Microsoft-Website im Artikel [Unterstützte Datentypen (Azure Search)].

Sie können die Art und Weise, in der Azure Search Daten für jede Instanz des Diensts partitioniert, nur in begrenztem Umfang steuern. In einer globalen Umgebung können Sie jedoch möglicherweise die Leistung verbessern sowie Latenz und Konflikte verringern, indem Sie den Dienst selbst mithilfe einer der folgenden Strategien partitionieren:

- Erstellen Sie eine Instanz von Azure Search in jeder geografischen Region, und stellen Sie sicher, dass Clientanwendungen an die nächste verfügbare Instanz geleitet werden. Diese Strategie erfordert, dass alle Aktualisierungen von durchsuchbarem Inhalt für alle Instanzen des Dienstes rechtzeitig repliziert werden.

- Erstellen Sie zwei Ebenen von Azure Search:
    - Einen lokalen Dienst in jeder Region, der die Daten enthält, auf die Benutzer in dieser Region am häufigsten zugreifen. Benutzer können auf dieser Ebene suchen, um schnelle, aber eingeschränkte Ergebnisse zu erhalten.
    - Einen globalen Dienst, der alle Daten enthält. Benutzer können auf dieser Ebene suchen, um langsamere, aber vollständigere Ergebnisse zu erhalten.

Dieser Ansatz ist besonders geeignet, wenn erhebliche regionale Unterschiede in Bezug auf die gesuchten Daten vorhanden sind.

## Partitionierungsstrategien für Azure Redis Cache

Azure Redis Cache bietet einen Shared Caching Service in der Cloud, der auf dem Redis-Schlüssel-Wert-Datenspeicher basiert. Wie der Name schon sagt, ist Azure Redis Cache als Cachinglösung konzipiert. Verwenden Sie den Cache nur zur vorübergehenden Speicherung, nicht als permanenten Datenspeicher. Anwendungen, die Azure Redis Cache verwenden, sollten weiterhin funktionieren, auch wenn der Cache nicht verfügbar ist. Azure Redis Cache unterstützt die primäre/sekundäre Replikation zur Gewährleistung hoher Verfügbarkeit, derzeit ist die maximale Cachegröße jedoch auf 53 GB beschränkt. Wenn mehr Speicherplatz benötigt wird, müssen Sie zusätzliche Caches erstellen. Weitere Informationen finden Sie auf der Microsoft-Website im Artikel [Azure Redis Cache].

Die Partitionierung eines Redis-Datenspeichers umfasst das Aufteilen von Daten über Instanzen des Redis-Diensts hinweg. Jede Instanz stellt eine einzelne Partition dar. Azure Redis Cache abstrahiert die Redis-Dienste hinter einer Fassade und zeigt sie nicht direkt an. Die einfachste Methode zum Implementieren der Partitionierung ist es, mehrere Azure Redis Cache-Instanzen zu erstellen und die Daten auf diese Instanzen zu verteilen.

Sie können jedem Datenelement einen Bezeichner (Partitionsschlüssel) zuordnen, der angibt, in welchem Cache es gespeichert ist. Die Logik der Clientanwendung kann diesen Bezeichner dann zum Weiterleiten von Anforderungen an die entsprechende Partition verwenden. Dieses Schema ist sehr einfach, aber wenn sich das Partitionierungsschema ändert (z. B. durch Erstellen zusätzlicher Azure Redis Cache-Instanzen), müssen Clientanwendungen möglicherweise neu konfiguriert werden.

Systemeigene Redis (nicht Azure Redis Cache) unterstützt die serverseitige Partitionierung basierend auf Redis-Clustering. Bei diesem Ansatz können Sie die Daten mit einem Hashmechanismus gleichmäßig auf die Server verteilen. Jeder Redis-Server speichert Metadaten, die den Bereich der Hash-Schlüssel beschreiben, die die Partition enthält, und enthält außerdem Informationen dazu, welche Hash-Schlüssel sich in den Partitionen auf anderen Servern befinden.

Clientanwendungen senden Anforderungen einfach an einen der teilnehmenden Redis-Server (wahrscheinlich an den nächstgelegenen). Der Redis-Server überprüft die Clientanforderung. Wenn die Anforderung lokal aufgelöst werden kann, führt der Redis-Server den angeforderten Vorgang aus. Andernfalls leitet er die Anforderung an den geeigneten Server weiter.

Dieses Modell wird mithilfe von Redis-Clustering implementiert und wird ausführlicher im [Redis-Cluster-Lernprogramm] auf der Redis-Website beschrieben. Redis-Clustering ist für Clientanwendungen transparent. Dem Cluster können zusätzliche Redis-Server hinzugefügt (und die Daten neu partitioniert) werden, ohne dass die Clients neu konfiguriert werden müssen.

> [AZURE.IMPORTANT] Azure Redis Cache unterstützt derzeit kein Redis-Clustering. Wenn Sie diesen Ansatz mit Azure umsetzen möchten, müssen Sie Ihre eigenen Redis-Server implementieren, indem Sie Redis auf einem Satz virtueller Azure-Computer installieren und diese manuell konfigurieren. Im Blog [Running Redis on a CentOS Linux VM in Azure] (Ausführen von Redis auf einer CentOS-Linux-VM in Azure) auf der Microsoft-Website finden Sie ein Beispiel für die Erstellung und Konfiguration eines Redis-Knotens, der als virtueller Azure-Computer ausgeführt wird.

Weitere Informationen zur Implementierung der Partitionierung mit Redis finden Sie auf der Redis-Website unter [Partitioning: how to split data among multiple Redis instances] (Partitionierung: Aufteilen von Daten auf mehrere Redis-Instanzen). Im restlichen Abschnitt wird davon ausgegangen, dass Sie clientseitige oder Proxy-unterstützte Partitionierung implementieren.

Berücksichtigen Sie folgende Punkte bei der Entscheidung, wie Daten mit Azure Redis Cache partitioniert werden sollen:

- Azure Redis Cache ist nicht als dauerhafter Datenspeicher gedacht, d. h. unabhängig vom Partitionierungsschema, das Sie implementieren, muss Ihr Anwendungscode Daten aus anderen Speicherorten als dem Cache abrufen können.
- Daten, auf die häufig zugegriffen wird, sollten in derselben Partition gespeichert werden. Redis ist ein leistungsfähiger Schlüssel-Wert-Speicher, der mehrere in hohem Maß optimierte Mechanismen zum Strukturieren von Daten bereitstellt. Bei diesen Mechanismen kann es sich um folgende handeln:
    - Einfache Zeichenfolgen (binäre Daten mit einer Länge von bis zu 512 MB)
    - Aggregierte Datentypen wie beispielsweise Listen (die als Warteschlangen und Stapel fungieren können)
    - Sätze (sortiert und nicht sortiert)
    - Hashes (die verwandte Felder gruppieren können, wie z. B. die Elemente, die die Felder in einem Objekt repräsentieren)

- Mithilfe der aggregierten Datentypen können Sie viele ähnliche Werte mit dem gleichen Schlüssel verknüpfen. Ein Redis-Schlüssel identifiziert eine Liste, einen Satz oder einen Hash anstatt der darin enthaltenen Datenelemente. All diese Datentypen sind mit Azure Redis Cache verfügbar und werden auf der Redis-Website unter [Data types] \(Datentypen) beschrieben. Beispielsweise können in dem Teil eines E-Commerce-Systems, in dem die Bestellungen von Kunden verfolgt werden, die Details der einzelnen Kunden in einem Redis-Hash gespeichert werden, der mithilfe der Kunden-ID verschlüsselt wurde. Jeder Hash kann eine Sammlung von Bestellnummern für den jeweiligen Kunden enthalten. Ein separater Redis-Satz kann die Bestellungen enthalten, die wieder als Hashes strukturiert sind und mithilfe der Bestellnummer verschlüsselt wurden. Abbildung 10 zeigt eine solche Struktur. Beachten Sie, dass Redis keine Form der referenziellen Integrität implementiert, daher ist es die Verantwortung des Entwicklers, die Beziehungen zwischen Kunden und Bestellungen zu verwalten.

![Vorgeschlagene Struktur im Redis-Speicher für die Aufzeichnung von Bestellungen und Informationen von Kunden](media/best-practices-data-partitioning/RedisCustomersandOrders.png)

_Abbildung 10: Vorgeschlagene Struktur im Redis-Speicher für die Aufzeichnung von Bestellungen und Informationen von Kunden_

> [AZURE.NOTE] In Redis sind alle Schlüssel binäre Datenwerte (wie Redis-Zeichenfolgen) und können bis zu 512 MB Daten enthalten. Theoretisch kann ein Schlüssel nahezu jegliche Informationen enthalten. Es empfiehlt sich jedoch, eine konsistente Benennungskonvention für Schlüssel anzuwenden, die den Datentyp beschreibt und die Entität identifiziert, aber nicht übermäßig lang ist. Eine gängige Methode ist die Verwendung von Schlüsseln im Format „Entitätstyp:ID“. Beispielsweise können Sie „Kunde:99“ verwenden, um den Schlüssel für einen Kunden mit der ID 99 anzugeben.

- Sie können die vertikale Partitionierung durch das Speichern von verwandten Informationen in anderen Aggregationen in derselben Datenbank implementieren. Sie können z. B. in einer E-Commerce-Anwendung häufig abgerufene Informationen zu Produkten in einem Redis-Hash speichern und weniger häufig verwendete Detailinformationen in einem anderen. Beide Hashes können die gleiche Produkt-ID als Teil des Schlüssels verwenden. Sie können z.B. „Produkt:_nn_“ für die allgemeinen Produktinformationen verwenden (_nn_ ist die Produkt-ID) und „Produktdetails:_nn_“ für die Detailinformationen. Mit dieser Strategie lässt sich die Datenmenge reduzieren, die bei den meisten Abfragen wahrscheinlich abgerufen werden.
- Sie können einen Redis-Datenspeicher neu partitionieren, denken Sie jedoch daran, dass dies eine komplexe und zeitaufwendige Aufgabe ist. Redis-Clustering kann Daten automatisch neu partitionieren, diese Funktion ist allerdings mit Azure Redis Cache nicht verfügbar. Daher sollten Sie beim Entwerfen Ihres Partitionierungsschemas für ausreichend freien Speicherplatz in jeder Partition sorgen, um den langfristig zu erwartenden Datenzuwachs zu ermöglichen. Denken Sie jedoch daran, dass Azure Redis Cache zur vorübergehenden Zwischenspeicherung von Daten vorgesehen ist, und dass im Cache gehaltene Daten eine begrenzte Lebensdauer haben, die als Time-to-live (TTL) Wert angegeben wird. Für relativ flüchtige Daten sollte der TTL-Wert klein, für statische Daten kann er jedoch wesentlich größer sein. Vermeiden Sie die Speicherung großer Mengen langlebiger Daten im Cache, wenn die Datenmenge wahrscheinlich den kompletten Cache belegen wird. Sie können eine Entfernungsrichtlinie angeben, die bewirkt, dass Azure Redis Cache die Daten entfernt, wenn der Speicherplatz knapp bemessen ist.

	> [AZURE.NOTE] Wenn Sie Azure Redis Cache verwenden, können Sie durch Auswahl des entsprechenden Tarifs die maximale Größe des Caches (von 250 MB bis 53 GB) angeben. Sie können einen Azure Redis Cache jedoch nach dem Erstellen nicht vergrößern (oder verkleinern).

- Redis-Batches und -Transaktionen können sich nicht über mehrere Verbindungen erstrecken, d. h. alle Daten, die von einem Batch- oder Transaktionsvorgang betroffen sind, sollten in der gleichen Datenbank (Shard) gespeichert werden.

	> [AZURE.NOTE] Eine Abfolge von Operationen in einer Redis-Transaktion ist nicht unbedingt atomar. Die Befehle, die eine Transaktion bilden, werden vor der Ausführung überprüft und in eine Warteschlange eingereiht. Wenn während dieser Phase ein Fehler auftritt, wird die gesamte Warteschlange verworfen. Nachdem die Transaktion jedoch erfolgreich übermittelt wurde, werden die Befehle in der Warteschlange der Reihenfolge nach ausgeführt. Wenn bei einem Befehl ein Fehler auftritt, wird nur die Ausführung dieses Befehls beendet. Alle vorherigen und nachfolgenden Befehle in der Warteschlange werden ausgeführt. Weitere Informationen finden Sie auf der Redis-Website unter [Transactions] \(Transaktionen).

- Redis unterstützt eine begrenzte Anzahl von atomischen Vorgängen. Die einzige Vorgänge dieser Art, die mehrere Schlüssel und Werte unterstützen, sind MGET und MSET. MGET-Vorgänge geben eine Auflistung von Werten für eine angegebene Liste von Schlüsseln zurück, und MSET-Vorgänge speichern eine Auflistung von Werten für eine angegebene Liste von Schlüsseln. Wenn Sie diese Vorgänge verwenden müssen, müssen die Schlüssel-Wert-Paare, auf die durch die MSET- und MGET-Befehle verwiesen wird, in derselben Datenbank gespeichert sein.

## Neuverteilen von Partitionen

Während ein System sich entwickelt und wenn Sie die Verwendungsmuster besser kennen, müssen Sie das Partitionierungsschema möglicherweise anpassen. Beispielsweise ist es möglich, dass in einzelnen Partitionen ein unverhältnismäßig großes Datenverkehrsvolumen auftritt, sodass diese Partitionen zu „heißen“ Partitionen werden, was zu übermäßigen Konflikten führt. Darüber hinaus haben Sie möglicherweise die Datenmenge in einigen Partitionen unterschätzt, wodurch nun die Grenzen der Speicherkapazität in diesen Partitionen erreicht werden. Unabhängig von den Ursachen, ist es manchmal notwendig, die Partitionen für einen ausgewogeneren Lastausgleich neu zu verteilen.

In einigen Fällen können Datenspeichersysteme, die nicht öffentlich anzeigen, wie Daten den Servern zugeordnet werden, Partitionen innerhalb der Grenzen der verfügbaren Ressourcen automatisch neu verteilen. In anderen Situationen ist das Neuverteilen eine administrative Aufgabe, die aus zwei Schritten besteht:

1. Festlegen der neuen Partitionierungsstrategie, um Folgendes zu ermitteln:
    - Welche Partitionen müssen aufgeteilt (oder möglicherweise kombiniert) werden?
    - Wie müssen die neuen Partitionsschlüssel entworfen werden, um diesen neuen Partitionen Daten zuzuordnen?
2. Migrieren der betroffenen Daten vom alten Partitionierungsschema in den neuen Satz von Partitionen.

> [AZURE.NOTE] Die Zuordnung der DocumentDB-Datenbanksammlungen zu Servern ist transparent, aber Sie können die Grenzen der Speicherkapazität und des Durchsatzes eines DocumentDB-Kontos dennoch erreichen. In diesem Fall müssen Sie Ihr Partitionierungsschema ändern und die Daten migrieren.

Je nach Datenspeicherungstechnologie und Entwurf Ihres Datenspeichersystems können Sie möglicherweise Daten zwischen Partitionen migrieren, während diese verwendet werden (Onlinemigration). Wenn dies nicht möglich ist, müssen Sie die Verfügbarkeit der betroffenen Partitionen vorübergehend aufheben, während die Daten migriert werden (Offlinemigration).

## Offlinemigration

Die Offlinemigration ist wohl die einfachste Vorgehensweise, da sie die Wahrscheinlichkeit von Konflikten reduziert. Nehmen Sie keine Änderungen an den Daten vor, während diese verschoben und neu strukturiert werden.

Dieser Prozess umfasst im Prinzip die folgenden Schritte:

1. Kennzeichnen Sie den Shard als offline.
2. Teilen Sie die Daten auf bzw. führen Sie sie zusammen, und verschieben Sie sie in die neuen Shards.
3. Überprüfen Sie die Daten.
4. Stellen Sie die neuen Shards online.
5. Entfernen Sie den alten Shard.

Um ein gewisses Maß an Verfügbarkeit beizubehalten, können Sie den ursprünglichen Shard in Schritt 1 als schreibgeschützt markieren, anstatt seine Verfügbarkeit aufzuheben. Auf diese Weise könnten Anwendungen die Daten während des Verschiebevorgangs lesen, jedoch nicht ändern.

## Onlinemigration

Eine Onlinemigration ist komplexer, doch für Benutzer weniger störend, da die Daten während des gesamten Vorgangs verfügbar bleiben. Der Prozess ähnelt der Offlinemigration, außer dass der ursprüngliche Shard nicht als offline (Schritt 1) gekennzeichnet wird. Je nach Granularität des Migrationsvorgangs (z. B. abhängig davon, ob der Vorgang Element für Element oder Shard für Shard ausgeführt wird) muss der Datenzugriffscode in den Clientanwendungen Daten an zwei Orten lesen und schreiben (im ursprünglichen und im neuen Shard).

Ein Beispiel für eine Lösung, die die Onlinemigration unterstützt, finden Sie auf der Microsoft-Website im Artikel [Skalierung mit dem Split-Merge-Tool für elastische Datenbanken].

## Zugehörige Muster und Anleitungen

Wenn Sie Strategien zum Implementieren der Datenkonsistenz in Betracht ziehen, können die folgenden Muster auch für Ihr Szenario relevant sein:

- Im Artikel [Data Consistency Primer] (Grundlagen der Datenkonsistenz) auf der Microsoft-Website werden Strategien für die Gewährleistung der Datenkonsistenz in einer verteilten Umgebung wie etwa der Cloud beschrieben.
- Im Artikel [Data Partitioning Guidance] (Anleitung zur Datenpartitionierung) auf der Microsoft-Website erhalten Sie eine allgemeine Übersicht über das Entwerfen von Partitionen, um zahlreiche Kriterien in einer verteilten Lösung zu erfüllen.
- Im Artikel [Sharding Pattern] \(Shardingmuster) auf der Microsoft-Website sind einige allgemeine Strategien für das Sharding von Daten zusammengefasst.
- Im Artikel [Index Table Pattern] (Indextabellenmuster) auf der Microsoft-Website wird veranschaulicht, wie Sie sekundäre Indizes für Daten erstellen. Mit diesem Ansatz kann eine Anwendung Daten durch Abfragen, die nicht auf den Primärschlüssel einer Sammlung verweisen, schnell abrufen.
- Im Artikel [Materialized View Pattern] (Muster für materialisierte Sichten) auf der Microsoft-Website wird erläutert, wie Sie vorab aufgefüllte Ansichten generieren können, die Daten zusammenfassen, um schnelle Abfragevorgänge zu unterstützen. Dieser Ansatz kann in einem partitionierten Datenspeicher hilfreich sein, wenn die Partitionen, die die Daten enthalten, die zusammengefasst werden, über mehrere Standorte verteilt sind.
- Der Artikel [Verwenden von Azure CDN] auf der Microsoft-Website stellt zusätzliche Anleitungen zum Konfigurieren und Verwenden eines Content Delivery Network mit Azure bereit.

## Weitere Informationen

- Im Artikel [Was ist SQL-Datenbank?] auf der Microsoft-Website finden Sie ausführliche Informationen zum Erstellen und Verwenden von SQL-Datenbanken.
- Die Seite [Übersicht über Features für elastische Datenbanken] auf der Microsoft-Website bietet eine umfassende Einführung in elastische Datenbanken.
- Im Artikel [Skalierung mit dem Split-Merge-Tool für elastische Datenbanken] auf der Microsoft-Website erhalten Sie Informationen zur Verwaltung elastischer Datenbanken mithilfe des Diensts zum Aufteilen/Zusammenführen.
- Im Artikel [Skalierbarkeits- und Leistungsziele für Azure Storage](https://msdn.microsoft.com/library/azure/dn249410.aspx) auf der Microsoft-Website sind die aktuellen Größen- und Durchsatzgrenzen vom Azure Storage dokumentiert.
- Im Artikel [Ausführen von Entitätsgruppentransaktionen] auf der Microsoft-Website erhalten Sie detaillierte Informationen zur Implementierung der Transaktionsvorgänge für Entitäten, die im Azure-Tabellenspeicher gespeichert sind.
- Im Artikel [Azure-Speichertabellen – Entwurfshandbuch] auf der Microsoft-Website finden Sie ausführliche Informationen zum Partitionieren von Daten im Azure-Tabellenspeicher.
- Im Artikel [Verwenden von Azure CDN] auf der Microsoft Website wird beschrieben, wie Daten in Azure-Blobspeichern mithilfe von Azure Content Delivery Network (CDN) repliziert werden können.
- Der Artikel [Verwalten von DocumentDB-Kapazitätsanforderungen] auf der Microsoft-Website enthält Informationen dazu, wie Azure-DocumentDB-Datenbanken Ressourcen zuordnen.
- Im Artikel [Was ist Azure Search?] auf der Microsoft-Website finden Sie eine vollständige Beschreibung der Funktionen von Azure Search.
- Im Artikel [Grenzwerte für den Azure Search-Dienst] auf der Microsoft-Website finden Sie Informationen zur Kapazität der einzelnen Instanzen von Azure Search.
- Im Artikel [Unterstützte Datentypen (Azure Search)] auf der Microsoft-Website sind die Datentypen zusammengefasst, die Sie in durchsuchbaren Dokumenten und Indizes verwenden können.
- Im Artikel [Azure Redis Cache] auf der Microsoft-Website erhalten Sie eine Einführung in Azure Redis Cache.
- Auf der Seite [Partitioning: how to split data among multiple Redis instances] (Partitionierung: Aufteilen von Daten auf mehrere Redis-Instanzen) auf der Redis-Website finden Sie weitere Informationen zur Implementierung der Partitionierung mit Redis.
- Im Blog [Running Redis on a CentOS Linux VM in Azure] (Ausführen von Redis auf einer CentOS-Linux-VM in Azure) auf der Microsoft-Website finden Sie ein Beispiel für die Erstellung und Konfiguration eines Redis-Knotens, der als virtueller Azure-Computer ausgeführt wird.
- Auf der Seite [Data Types] \(Datentypen) auf der Redis-Website werden die Datentypen beschrieben, die mit Redis und Azure Redis Cache verfügbar sind.

[Azure Redis Cache]: http://azure.microsoft.com/services/cache/
[Skalierbarkeits- und Leistungsziele für Azure Storage]: storage/storage-scalability-targets.md
[Azure-Speichertabelle – Entwurfshandbuch]: storage/storage-table-design-guide.md
[Azure-Speichertabellen – Entwurfshandbuch]: storage/storage-table-design-guide.md
[Building a Polyglot Solution]: https://msdn.microsoft.com/library/dn313279.aspx
[Data Access for Highly-Scalable Solutions: Using SQL, NoSQL, and Polyglot Persistence]: https://msdn.microsoft.com/library/dn271399.aspx
[Data consistency primer]: http://aka.ms/Data-Consistency-Primer
[Data Partitioning Guidance]: https://msdn.microsoft.com/library/dn589795.aspx
[Data Types]: http://redis.io/topics/data-types
[DocumentDB-Einschränkungen und Kontingente]: documentdb/documentdb-limits.md
[DocumentDB-Grenzen und -Kontingente]: documentdb/documentdb-limits.md
[Übersicht über Features für elastische Datenbanken]: sql-database/sql-database-elastic-scale-introduction.md
[Federations Migration Utility]: https://code.msdn.microsoft.com/vstudio/Federations-Migration-ce61e9c1
[Index Table Pattern]: http://aka.ms/Index-Table-Pattern
[Verwalten von DocumentDB-Kapazitätsanforderungen]: documentdb/documentdb-manage.md
[Weitere Informationen zu Kapazität und Dokumentspeicherung in DocumentDB]: documentdb/documentdb-manage.md
[Materialized View Pattern]: http://aka.ms/Materialized-View-Pattern
[Abfragen von mehreren Shards]: sql-database/sql-database-elastic-scale-multishard-querying.md
[Partitioning: how to split data among multiple Redis instances]: http://redis.io/topics/partitioning
[Leistungsebenen in DocumentDB]: documentdb/documentdb-performance-levels.md
[Ausführen von Entitätsgruppentransaktionen]: https://msdn.microsoft.com/library/azure/dd894038.aspx
[Redis-Cluster-Lernprogramm]: http://redis.io/topics/cluster-tutorial
[Running Redis on a CentOS Linux VM in Azure]: http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx
[Skalierung mit dem Split-Merge-Tool für elastische Datenbanken]: sql-database/sql-database-elastic-scale-overview-split-and-merge.md
[Verwenden von Azure CDN]: cdn/cdn-create-new-endpoint.md
[Service Bus-Kontingente]: service-bus/service-bus-quotas.md
[Grenzwerte für den Azure Search-Dienst]: search/search-limits-quotas-capacity.md
[Grenzwerte für den Azure Search-Dienst]: search/search-limits-quotas-capacity.md
[Sharding pattern]: http://aka.ms/Sharding-Pattern
[Unterstützte Datentypen (Azure Search)]: https://msdn.microsoft.com/library/azure/dn798938.aspx
[Transactions]: http://redis.io/topics/transactions
[Was ist Azure Search?]: search/search-what-is-azure-search.md
[Was ist SQL-Datenbank?]: sql-database/sql-database-technical-overview.md

<!---HONumber=AcomDC_0810_2016-->