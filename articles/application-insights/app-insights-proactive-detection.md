<properties 
	pageTitle="Proaktive Erkennung in Application Insights | Microsoft Azure" 
	description="Application Insights führt eine automatische umfassende Analyse Ihrer App-Telemetrie durch und warnt Sie vor potenziellen Problemen." 
	services="application-insights" 
    documentationCenter="windows"
	authors="rakefetj" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="awills"/>

#  Proaktive Erkennung in Application Insights

 Die proaktive Erkennung warnt Sie automatisch vor potenziellen Leistungsproblemen in Ihrer Webanwendung. Sie führt eine intelligente Analyse der Telemetriedaten durch, die Ihre App an [Visual Studio Application Insights](app-insights-overview.md) sendet. Bei einem plötzlichen Anstieg der Fehlerraten oder bei ungewöhnlichen Mustern in der Client- oder Serverleistung erhalten Sie eine Warnung. Diese Funktion muss nicht konfiguriert werden. Sie wird ausgeführt, wenn Ihre Anwendung genügend Telemetriedaten sendet.

Sie können auf Warnungen der proaktiven Erkennung über die erhaltenen E-Mails oder das Blatt für die proaktive Erkennung zugreifen.



## Überprüfen proaktiver Erkennungen

Sie haben zwei Möglichkeiten, Erkennungen anzuzeigen:

* **Sie erhalten eine E-Mail** von Application Insights. Hier sehen Sie ein typisches Beispiel:

    ![E-Mail-Warnung](./media/app-insights-proactive-detection/03.png)

    Klicken Sie auf die große Schaltfläche, um ausführlichere Informationen im Portal zu öffnen.

* Die Kachel **Proaktive Erkennung** auf dem Blatt „Übersicht“ Ihrer App zeigt die Anzahl aktueller Warnungen. Klicken Sie auf die Kachel, um eine Liste der aktuellen Warnungen anzuzeigen.

![Aktuelle Erkennungen anzeigen](./media/app-insights-proactive-detection/04.png)

Wählen Sie eine Warnung aus, um Details anzuzeigen.


## Welche Probleme werden erkannt?

Es gibt drei Arten der Erkennung:

* [Fehlerwarnungen nahezu in Echtzeit](app-insights-proactive-failure-diagnostics.md). Wir nutzen Machine Learning, um die voraussichtliche Rate fehlerhafter Anforderungen für Ihre App (korreliert mit Lastangaben und anderen Faktoren) festzulegen. Falls die Fehlerrate den erwarteten Rahmen überschreitet, wird eine Warnung gesendet.
* [Anomaliediagnose](app-insights-proactive-anomaly-diagnostics.md). Wir suchen jeden Tag nach anormalem Verhalten bei Reaktionszeiten und Fehlerraten. Diese Probleme werden mit Eigenschaften wie Standort, Browser, Clientbetriebssystem, Serverinstanz und Tageszeit in Beziehung gesetzt.
* [Azure Cloud Services](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Sie erhalten Warnungen, wenn Ihre App in Azure Cloud Services gehostet wird und bei einer Rolleninstanz Startfehler, häufige Wiederverwendungen oder Abstürze zur Laufzeit auftreten.

(Über die Hilfelinks in den jeweiligen Benachrichtigungen gelangen Sie zu den relevanten Artikeln.)


## Nächste Schritte

Mit diesen Diagnosetools können Sie die Telemetriedaten Ihrer App untersuchen:

* [Metrik-Explorer](app-insights-metrics-explorer.md)
* [Suchexplorer](app-insights-diagnostic-search.md)
* [Analytics: Leistungsfähige Abfragesprache](app-insights-analytics-tour.md)

Proaktive Erkennungen sind vollkommen automatisch. Vielleicht möchten Sie aber weitere Warnungen einrichten?

* [Einrichten von Warnungen in Application Insights](app-insights-alerts.md)
* [Verfügbarkeitswebtests](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->