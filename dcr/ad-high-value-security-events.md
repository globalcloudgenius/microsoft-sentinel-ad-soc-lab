# AD High-Value Security Event DCR

The domain controller DCR is designed to collect a focused Windows Security event set into the Sentinel workspace.

Azure Monitor limits the number of expressions in a single XPath filter. The event set was therefore split across two XPath queries.

## DCR data source

```text
Name: CG-AD-HighValue-Security
Stream: Microsoft-SecurityEvent
Destination: Log Analytics / Microsoft Sentinel
```

## XPath query 1

```text
Security!*[System[(EventID=1102 or EventID=4625 or EventID=4648 or EventID=4672 or EventID=4697 or EventID=4719 or EventID=4720 or EventID=4722 or EventID=4723 or EventID=4724 or EventID=4725 or EventID=4726 or EventID=4727 or EventID=4728 or EventID=4729 or EventID=4732 or EventID=4733)]]
```

## XPath query 2

```text
Security!*[System[(EventID=4735 or EventID=4737 or EventID=4739 or EventID=4740 or EventID=4742 or EventID=4754 or EventID=4755 or EventID=4756 or EventID=4757 or EventID=4767 or EventID=4781 or EventID=4799 or EventID=5136 or EventID=5137 or EventID=5139 or EventID=5141)]]
```

## Association

The DCR was associated directly with the Arc-enabled domain controller.

The association was verified by listing data-collection rule associations for the machine and confirming the dedicated AD DCR was returned.
