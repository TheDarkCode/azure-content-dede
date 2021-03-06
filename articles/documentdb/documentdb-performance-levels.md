<properties
	pageTitle="Leistungsebenen in DocumentDB | Microsoft Azure"
	description="Erfahren Sie, wie Sie mithilfe von Leistungsebenen in DocumentDB den Durchsatz pro Sammlung reservieren können."
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2016"
	ms.author="mimig"/>

# Leistungsebenen in DocumentDB

Dieser Artikel bietet einen Überblick über die Leistungsebenen in [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

-	Was ist eine Leistungsebene?
-	Wie lässt sich der Durchsatz für ein Datenbankkonto reservieren?
-	Wie arbeite ich mit Leistungsebenen?
-	Wie werden Leistungsebenen abgerechnet?

## Einführung in Leistungsebenen

Jede DocumentDB-Sammlung, die unter einem Standardkonto erstellt wird, wird mit einer zugewiesenen Leistungsebene bereitgestellt. Jede Sammlung in einer Datenbank kann über individuelle Leistungsebenen verfügen. So können Sie häufig genutzten Sammlungen mehr Durchsatz und seltener genutzten Sammlungen weniger Durchsatz zuweisen. DocumentDB unterstützt sowohl benutzerdefinierte als auch vordefinierte Leistungsebenen.

Jede Leistungsebene verfügt über ein zugehöriges Datenübertragungslimit der [Anforderungseinheit](documentdb-request-units.md) (Request Unit, RU). Dies ist der Durchsatz, der für die Sammlung basierend auf ihrer Leistungsebene reserviert wird und ausschließlich von dieser Sammlung verwendet werden kann.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
        	<td valign="top"><p></p></td>
            <td valign="top"><p>Details</p></td>
            <td valign="top"><p>Durchsatzlimit</p></td>
            <td valign="top"><p>Speichergrenzwerte</p></td>
            <td valign="top"><p>Version</p></td>
 			<td valign="top"><p>APIs</p></td>            
        </tr>
        <tr>
        	<td valign="top"><p>Benutzerdefinierte Leistung</p></td>
            <td valign="top"><p>Speicher auf Grundlage des Verbrauchs in GB bemessen.</p><p>Durchsatz in Einheiten von 100 RU/s</p></td>
            <td valign="top"><p>Unbegrenzt. standardmäßig 400 - 250.000 RU/s (höher auf Anforderung)</p></td>
            <td valign="top"><p>Unbegrenzt. standardmäßig 250 GB (höher auf Anforderung) </p></td>
            <td valign="top"><p>V2</p></td>
 			<td valign="top"><p>API 2015-12-16 und neuer</p></td>  
        </tr>
        <tr>
        	<td valign="top"><p>Vordefinierte Leistung</p></td>
            <td valign="top"><p>10 GB reservierter Speicher.</p><p>S1 = 250 RU/s, S2 = 1.000 RU/s, S3 = 2.500 RU/s</p></td>
            <td valign="top"><p>2.500 RU/s</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>V1</p></td>
 			<td valign="top"><p>Beliebig</p></td>  
        </tr>        
    </tbody>
</table>                


DocumentDB ermöglicht eine Vielzahl von Datenbankvorgängen, wie z. B. Abfragen, Abfragen mit benutzerdefinierten Funktionen (UDFs), gespeicherte Prozeduren und Trigger. Die Verarbeitungskosten im Zusammenhang mit diesen Vorgängen variieren basierend auf CPU, E/A und Speicher, die/der für den jeweiligen Vorgang erforderlich ist. Anstatt sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

Sammlungen können über das [Microsoft Azure-Portal](https://portal.azure.com), die [REST-API](https://msdn.microsoft.com/library/azure/mt489078.aspx) oder jedes der [DocumentDB SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) erstellt werden. Mithilfe der DocumentDB-APIs können Sie die Leistungsebene einer Sammlung festlegen.

> [AZURE.NOTE] Die Leistungsebene einer Sammlung kann über die APIs oder das [Microsoft Azure-Portal ](https://portal.azure.com/) angepasst werden. Änderungen der Leistungsstufe sollten innerhalb von drei Minuten abgeschlossen sein.

## Festlegen von Leistungsebenen für Sammlungen
Sobald eine Sammlung erstellt wurde, wird die vollständige Zuweisung von RUs basierend auf der angegebenen Leistungsebene für die Sammlung reserviert.

Beachten Sie, dass DocumentDB sowohl über benutzerdefinierte als auch über vordefinierte Leistungsebenen verfügt und basierend auf dem reservierten Durchsatz betrieben wird. Durch Erstellen einer Sammlung reserviert eine Anwendung Durchsatz, welcher entsprechend in Rechnung gestellt wird. Dies geschieht unabhängig davon, wie viel von diesem Durchsatz aktiv genutzt wird. Der Speicher wird bei benutzerdefinierten Leistungsebenen auf Basis des Verbrauchs gemessen. Bei vordefinierten Leistungsebenen hingegen werden im Moment der Erstellung der Sammlung 10 GB Speicherplatz reserviert.

Nach dem Erstellen einer Sammlung können Sie die Leistungsebene über die DocumentDB-SDKs oder das klassische Azure-Portal anpassen.

> [AZURE.IMPORTANT] DocumentDB-Standardsammlungen werden auf Stundenbasis abgerechnet. Hierbei wird für jede erstellte Sammlung eine Verwendung von mindestens einer Stunde berechnet.

Wenn Sie die Leistungsebene einer Sammlung innerhalb einer Stunde anpassen, wird die höchste, während dieser Stunde festgelegte Leistungsebene berechnet. Beispiel: Wenn Sie die Leistungsebene für eine Sammlung um 8:53 Uhr erhöhen, wird die neue Ebene ab 8:00 Uhr berechnet. Wenn Sie die Leistungsebene um 8:53 Uhr verringern, gilt der neue Satz ab 9:00 Uhr.

Anforderungseinheiten werden für jede Sammlung basierend auf der Leistungsebene reserviert. Der Verbrauch von Anforderungseinheiten wird als Pro-Sekunde-Rate bemessen. Anwendungen, die die bereitgestellte Anforderungseinheitsrate (oder Leistungsebene) einer Sammlung überschreiten, werden gedrosselt, bis die Rate unter das für die Sammlung reservierte Niveau fällt. Wenn für Ihre Anwendung ein höherer Durchsatz erforderlich ist, können Sie die Leistungsebene für jede Sammlung erhöhen.

> [AZURE.NOTE] Wenn Ihre Anwendung die Leistungsebene für eine oder mehrere Sammlungen überschreitet, werden die Anforderungen pro Sammlung beschränkt. Dies bedeutet, dass einige Anforderungen erfolgreich sind, während andere aufgrund der Beschränkung fehlschlagen. Es wird empfohlen, bei einer Beschränkung eine kleine Anzahl von Wiederholungen hinzuzufügen, um die Spitzen im Datenverkehr zu bewältigen.

## Arbeiten mit Leistungsebenen
Mit DocumentDB-Sammlungen können Sie Ihre Daten basierend auf den Abfragemustern und Leistungsanforderungen Ihrer Anwendung gruppieren. Aufgrund der DocumentDB-Unterstützung für automatische Indizierung und Abfragen ist es üblich, heterogene Dokumente in derselben Sammlung zusammenzustellen. Die wichtigsten Überlegungen bei der Entscheidung für oder gegen die Verwendung separater Sammlungen umfassen:

- Abfragen – eine Sammlung ist der Bereich für die Abfrageausführung. Wenn Sie eine Abfrage über eine Reihe von Dokumenten hinweg durchführen müssen, sind Lesevorgänge effizienter, wenn diese Dokumente in einer einzigen Sammlung zusammengestellt werden.
- Transaktionen – Alle Transaktionen sind auf den Inhalt einer Sammlung beschränkt. Wenn einige Ihrer Dokumente innerhalb einer einzelnen, gespeicherten Prozedur oder eines Triggers aktualisiert werden müssen, müssen sie in der gleichen Sammlung gespeichert werden. Genauer gesagt ist ein Partitionsschlüssel innerhalb einer Sammlung die Transaktionsgrenze. Detaillierte Ausführungen finden Sie unter [Partitionieren in DocumentDB](documentdb-partition-data.md).
- Leistungsisolation – Eine Sammlung verfügt über eine zugewiesene Leistungsebene. So wird sichergestellt, dass jede Sammlung eine vorhersagbare Leistung durch reservierte RUs erbringt. Daten können verschiedenen Sammlungen mit unterschiedlichen Leistungsebenen (basierend auf der Zugriffshäufigkeit) zugeordnet werden.

> [AZURE.IMPORTANT] Es ist wichtig, zu verstehen, dass die Abrechnung zu vollständigen Standardsätzen basierend auf der Anzahl der von Ihrer Anwendung erstellten Sammlungen erfolgt.

Es wird empfohlen, dass die Anwendung eine kleine Anzahl von Sammlungen verwendet, sofern Sie nicht über große Speicher oder Durchsatzanforderungen verfügen. Stellen Sie sicher, dass Sie Anwendungsmuster für die Erstellung von neuen Sammlungen gut verstanden haben. Sie können die Sammlungserstellung als Verwaltungsaktion reservieren, die außerhalb Ihrer Anwendung durchgeführt wird. Ähnlich ändert sich der Stundensatz, über den die Sammlung abgerechnet wird, wenn Sie die Leistungsebene für eine Sammlung anpassen. Wenn Ihre Anwendung die Leistungsebenen dynamisch anpasst, sollten Sie diese überwachen.

## <a id="changing-performance-levels-using-the-azure-portal"></a>Wechseln von S1, S2, S3 zu benutzerdefinierter Leistung

Führen Sie die hier beschriebenen Schritte durch, um im Azure-Portal von vordefinierten Durchsatzstufen zu benutzerdefinierten Durchsatzstufen zu wechseln. Bei Verwendung benutzerdefinierter Durchsatzstufen können Sie den Durchsatz an Ihre Anforderungen anpassen. Wenn Sie immer noch ein S1-Konto verwenden, können Sie den Standarddurchsatz mit wenigen Klicks von 250 RU/s auf 400 RU/s erhöhen.

Weitere Informationen zu Preisänderungen in Verbindung mit benutzerdefiniertem und vordefiniertem Durchsatz finden Sie im Blogbeitrag [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/) (DocumentDB: Alles Wissenswerte zur Verwendung der neuen Preisoptionen).

> [AZURE.VIDEO changedocumentdbcollectionperformance]

1. Navigieren Sie in Ihrem Browser zum [**Azure-Portal**](https://portal.azure.com).
2. Klicken Sie auf **Durchsuchen** > **DocumentDB-Konten**, und wählen Sie das zu ändernde DocumentDB-Konto aus.
3. Wählen Sie im Fokus **Datenbanken** die zu ändernde Datenbank und anschließend auf dem Blatt **Datenbank** die zu ändernde Sammlung aus. Für Konten mit vordefiniertem Durchsatz gilt der Tarif S1, S2 oder S3.

      ![Screenshot des Blatts „Datenbank“ mit einer S1-Sammlung](./media/documentdb-performance-levels/documentdb-change-performance-S1.png)

4. Klicken Sie auf dem Blatt **Sammlungen** auf **Weiter** und dann auf der oberen Leiste auf **Einstellungen**.
5. Klicken Sie auf dem Blatt **Einstellungen** auf **Tarif**. Die voraussichtlichen monatlichen Kosten für die einzelnen Tarife werden auf dem Blatt **Tarif auswählen** angezeigt. Klicken Sie für den Wechsel zu benutzerdefiniertem Durchsatz auf **Standard** und anschließend zum Speichern der Änderung auf **Auswählen**.

      ![Screenshot der DocumentDB-Blätter „Einstellungen“ und „Preisstufe auswählen“](./media/documentdb-performance-levels/documentdb-change-performance.png)

6. Auf dem Blatt **Einstellungen** wurde der **Tarif** in **Standard** geändert, und im Feld **Durchsatz (RU/s)** wird als Standardwert „400“ angezeigt. Legen Sie den Durchsatz auf einen Wert zwischen 400 und 10.000 [Anforderungseinheiten](documentdb-request-units.md)/Sekunde (RU/s) fest. Die **Preiszusammenfassung** am unteren Rand der Seite wird automatisch aktualisiert, um eine Schätzung der monatlichen Kosten anzugeben. Klicken Sie zum Speichern der Änderungen auf **OK**.

	![Screenshot des Blatts „Einstellungen“, der veranschaulicht, wo Sie den Durchsatzwert ändern können](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

7. Auf dem Blatt **Datenbank** können Sie den neuen Durchsatz der Sammlung überprüfen.

	![Screenshot des Blatts „Datenbank“ mit geänderter Sammlung](./media/documentdb-performance-levels/documentdb-change-performance-confirmation.png)

Wenn Sie feststellen, dass Sie einen höheren Durchsatz (größer als 10.000 RU/s) oder mehr Speicher (größer als 10 GB) benötigen, können Sie eine partitionierte Sammlung erstellen. Weitere Informationen zum Erstellen einer partitionierten Sammlung finden Sie unter [So erstellen Sie eine DocumentDB-Sammlung über das Azure-Portal](documentdb-create-collection.md).

>[AZURE.NOTE] Das Ändern von Leistungsstufen einer Sammlung kann bis zu 2 Minuten dauern.

## Ändern von Leistungsstufen mit dem .NET SDK

Eine weitere Möglichkeit zum Ändern der Leistungsstufen Ihrer Sammlungen stellen unsere SDK dar. In diesem Abschnitt wird nur das Ändern einer Sammlung mit unserer [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) behandelt. Die Vorgehensweise bei unseren anderen [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) ist jedoch ähnlich. Wenn Sie noch keine Erfahrung mit unserer .NET SDK haben, besuchen Sie unser [Tutorial "Erste Schritte"](documentdb-get-started.md).

Nachstehend finden Sie einen Codeausschnitt zum Ändern des Angebotsdurchsatzes auf 50.000 Anforderungseinheiten pro Sekunde:

	//Fetch the resource to be updated
	Offer offer = client.CreateOfferQuery()
		              .Where(r => r.ResourceLink == collection.SelfLink)    
		              .AsEnumerable()
		              .SingleOrDefault();

	// Set the throughput to 5000 request units per second
	offer = new OfferV2(offer, 5000);

	//Now persist these changes to the database by replacing the original resource
	await client.ReplaceOfferAsync(offer);

	// Set the throughput to S2
	offer = new Offer(offer);
	offer.OfferType = "S2";

	//Now persist these changes to the database by replacing the original resource
	await client.ReplaceOfferAsync(offer);



> [AZURE.NOTE] Sammlungen mit weniger als 10.000 Anforderungseinheiten pro Sekunde können zwischen Angeboten mit benutzerdefiniertem und vordefiniertem Durchsatz (S1, S2, S3) zu jedem beliebigen Zeitpunkt migriert werden. Sammlungen mit mehr als 10.000 Anforderungseinheiten pro Sekunde können nicht in vordefinierte Durchsatzebenen konvertiert werden.

Besuchen Sie [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx), um weitere Beispiele anzuzeigen und mehr über unsere Angebotsmethoden zu erfahren:

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## <a id="change-throughput"></a>Ändern des Durchsatzes einer Sammlung

Wenn Sie bereits benutzerdefinierte Leistung nutzen, können Sie den Durchsatz der Sammlung wie folgt ändern. Wenn Sie von einer Leistungsebene des Typs S1, S2 oder S3 (vordefinierte Leistung) zu benutzerdefinierter Leistung wechseln müssen, siehe [Wechsel von den Leistungsebenen S1, S2 und S3 zu benutzerdefinierter Leistung](#changing-performance-levels-using-the-azure-portal).

1. Navigieren Sie in Ihrem Browser zum [**Azure-Portal**](https://portal.azure.com).
2. Klicken Sie auf **Durchsuchen** > **DocumentDB-Konten**, und wählen Sie das zu ändernde DocumentDB-Konto aus.
3. Wählen Sie auf dem Blatt **DocumentDB-Konto** im Fokus **Datenbanken** die zu ändernde Datenbank und anschließend auf dem Blatt **Datenbank** die zu ändernde Sammlung aus.
4. Klicken Sie auf dem Blatt **Sammlungen** auf der oberen Leiste auf **Einstellungen**.
5. Erhöhen Sie auf dem Blatt **Einstellungen** den Wert im Feld **Durchsatz (RU/Sekunde)**, und klicken Sie dann auf **OK**, um die Änderung zu speichern. Das Feld **Preiszusammenfassung** unten auf dem Blatt wird in die neuen geschätzten monatlichen Kosten dieser Sammlung in einer einzelnen Region geändert.

    ![Screenshot des Blatts „Einstellungen“ mit hervorgehobenem Feld „Durchsatz“ und Preiszusammenfassung](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

Wenn Sie nicht sicher sind, um wie viel Sie den Durchsatz erhöhen sollten, siehe [Schätzen der Durchsatzanforderungen](documentdb-request-units.md#estimating-throughput-needs) und [Rechner für Anforderungseinheiten](https://www.documentdb.com/capacityplanner).

## Nächste Schritte

Weitere Informationen zu Preisen und der Datenverwaltung mit Azure DocumentDB finden Sie in folgenden Ressourcen:

- [DocumentDB-Preise](https://azure.microsoft.com/pricing/details/documentdb/)
- [Verwalten der DocumentDB-Kapazität](documentdb-manage.md)
- [Modellieren von Daten in DocumentDB](documentdb-modeling-data.md)
- [Partitionieren von Daten in DocumentDB](documentdb-partition-data.md)
- [Anforderungseinheiten](http://go.microsoft.com/fwlink/?LinkId=735027)

Weitere Informationen zu DocumentDB finden Sie in der Azure DocumentDB-[Dokumentation](https://azure.microsoft.com/documentation/services/documentdb/).

Im Artikel [Leistungs- und Skalierungstests mit Azure DocumentDB](documentdb-performance-testing.md) finden Sie eine Einführung in Leistungs- und Skalierungstests mit DocumentDB.

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png

<!---HONumber=AcomDC_0831_2016-->