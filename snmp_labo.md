# SNMP

* Geencodeerd in ASN.1
* https://tools.ietf.org/html/rfc1592 (p11)
* TLV Tripletten (Tag-Length-Value)
* BER van NMSs en agents vertalen dit

## Tags

* Wat gaan we versturen?
* NULL bestaat om TLV zonder value veld!

![tag](https://lh3.googleusercontent.com/cpw6C2E5S8EBnwmKadvbr1cDYO9uYvrnCtVuJ28jfis=s350 "tag.png")

| Class            | Name           | bit       | H   |
| ---------------- | -------------- | --------- | --- |
| Universal        | Integer        | 0000 0010 | 02H |
| Universal        | Octet String   | 0000 0100 | 04H |
| Universal        | NULL           | 0000 0101 | 05H |
| Universal        | OID            | 0000 0110 | 06H |
| Universal        | SEQUENCE       | 0011 0000 | 30H |
| Universal        | SEQUENCE OF    | 0011 0000 | 30H |
| Application      | Ipaddress      | 0100 0000 | 40H |
| Application      | Counter        | 0100 0001 | 41H |
| Application      | TimeTicks      | 0100 0011 | 43H |
| Context-specific | GetRequest     | 1010 0000 | A0H |
| Context-specific | GetNextRequest | 1010 0001 | A1H |
| Context-specific | GetResponse    | 1010 0010 | A2H |
| Context-specific | SetRequest     | 1010 0011 | A3H |
| Context-specific | TrapResponse   | 1010 0100 | A4H |
| Context-specific | GetBulkRequest | 1010 0101 | A5H |
| Context-specific | InformRequest  | 1010 0110 | A6H |
| Context-specific | SNMPv2Trap     | 1010 0111 | A7H |

<p style="page-break-after:always;"></p>
## Length
* Lengte van het waardeveld
* Als > 128 dan 1n|xxxxxxxx|xxxxxxxx|... (n = aantal bytes)
* Als <= 127, 1 byte met lengte

![length](https://lh3.googleusercontent.com/uoUBlPjBz9B-c4G5_3t5KjdCq2rfRwYGHhmxi7Vtw8A=s350 "length.png")

Vb:

* raz
  _ 04H (Octet String) 03H (Length)
  _ 03H = 3 => 3 lang
* oid 1.3.6.1.2.1.1
  _ 06H (OID) 06H (Length)
  _ 2BH (1.3) 06H (6) 01H (1) 02H (2) 01H (1) 01H (1) ==> 1.3.6.1.2.1.1
* SEQUENCE OF
  _ 30H (Sequence) 33H (Length = 51)
  _ 06H (OID) 08H (8) ... \* 04H (Integer) 27H (39) ...
* IP Address
  _ 40H (IPAddress) 04H (Length = 4)
  _ 80H (128) 96H (150) A1H (161) 09H (9) (128.150.161.9)

## Diagram

![diagram](https://lh3.googleusercontent.com/ejMj28QGT4M0SL22vXRaYjIsgOWBtmInmWs0H2x51V4=s1000 "snmp_diagram.png")

<p style="page-break-after:always;"></p>
## SNMP Message : GetRequest 
### Definitie
	GetRequestMessage ::= SEQUENCE {
		version INTEGER,
		community OCTET STRING,
		protocolDataUnit SEQUENCE {
			requestDataUnit SEQUENCE {
				requestID INTEGER,
				errorStatus INTEGER (0-5),
				errorIndex INTEGER,
				varBindList SEQUENCE OF {
					varBind SEQUENCE {
						name OBJECT IDENTIFIER,
						value ObjectSyntax
					}
				}
			}
		}
	}

### Error Values

| Error      | Value | Description                                   |
| ---------- | ----- | --------------------------------------------- |
| noError    | 0     | Vraag kan beantwoord worden                   |
| tooBig     | 1     | antwoord te groot om in UDP verpakt te worden |
| noSuchName | 2     | 1 van de gevraagde OIDs bestaat niet          |
| genErr     | 5     | alle overige fouten                           |

### Voorbeeld 1 (OID < 128)

    30 27 (SEQUENCE Lengte 27)
    		02 01 00 (Integer Lengte 1 Version 0)
    		04 06 p u b l i c (Octet String Lengte 6 "public")
    		a0 1a (GetRequest Lengte 26)
    				02 02 1b e7 (Integer Lengte 2 (27 en 231) == requestID)
    				02 01 00 (Integer Lengte 1 0 == errorStatus)
    				02 01 00 (Integer Lengte 1 0 == errorIndex)
    				30 0e (SEQUENCE Lengte 14 == varBindList)
    						30 0c (SEQUENCE Lengte 12 == varBind)
    								06 08 2b 06 01 02 01 04 01 00 (OID Lengte 8 "1.3.6.1.2.1.4.1.0")
    								05 00 (NULL lengte 0 (Waarde object niet gekend))

### Voorbeeld 2 (OID >= 128)

    30 27 (SEQUENCE Lengte 27)
    		02 01 00 (Integer Lengte 1 Version 0)
    		04 06 p u b l i c (Octet String Lengte 6 "public")
    		a0 1a (GetRequest Lengte 26)
    				02 02 1b e7 (Integer Lengte 2 (27 en 231) == requestID)
    				02 01 00 (Integer Lengte 1 0 == errorStatus)
    				02 01 00 (Integer Lengte 1 0 == errorIndex)
    				30 0e (SEQUENCE Lengte 14 == varBindList)
    						30 0c (SEQUENCE Lengte 12 == varBind)
    								06 0c 2b 06 01 04 01 82 37 01 03 01 06 00 (OID Lengte 12 "1.3.6.1.4.1.311.1.3.1.6")
    								05 00 (NULL lengte 0 (Waarde object niet gekend))

### Voorbeeld 3 (Lengte > 128)

04 81 ea a z e r t y . . . w x c v d n

04 = Octet String
81 = 1000 0001 wat n = 1 geeft en dus 1 byte geeft als lengte (MSB is altijd 1)
EA = 234

### Opmerkingen

> * **2b** in varBind is 1.3 !!!
> * Eerste 2 bytes worden samen geëncodeerd.
> * Als >= 128, dan component in 7 laagste bits van 2 bytes.
> * Bij GET is waarde object niet gekend, dus stuur NULL terug.
>   82 37 = 311 want 82 = 1000 0010 en 37 = 0011 0111, eerste bit eerste byte moet 0 worden wat 10 0011 0111 = 311 geeft.
>   Lengtes tellen is enkel de value bytes!

## SNMP Message : TrapResponse

### Definitie

    TrapResponseMessage ::= SEQUENCE {
    	version INTEGER (0),
    	community OCTET STRING,
    	protocolDataUnit SEQUENCE {
    		enterpriseID OBJECT IDENTIFIER,
    		agentAddress IpAddress,
    		genericTrap INTEGER,
    		specificTrap INTEGER,
    		timeStamp TimeTicks,
    		varBindList SEQUENCE OF {
    			varBind SEQUENCE {
    				name OBJECT IDENTIFIER,
    				value ObjectSyntax
    			}
    		}
    	}
    }

## SNMP Message : GetBulkRequest

### Definitie

    GetBulkRequestMessage ::= SEQUENCE {
    	version INTEGER (0),
    	community OCTET STRING,
    	protocolDataUnit SEQUENCE {
    		requestID INTEGER,
    		nonRepeaters INTEGER,
    		maxRepetitions INTEGER,
    		varBindList SEQUENCE OF {
    			varBind SEQUENCE {
    				name OBJECT IDENTIFIER,
    				value ObjectSyntax
    			}
    		}
    	}
    }

<p style="page-break-after:always;"></p>
## Commando's
```bash
net hexdump -c bestand # Print bestand
nc -uvw1 localhost 161 < bestand -o con: > nul: #(> con: > nul: stuurt output naar NULL)
snmpgetnext localhost .1 # Vind volgende OID achter .1
snmpgetnext localhost sysDescr.0 # Vind volgende OID na sysDescr
snmpwalk -d localhost svSvcName # Geeft alles van svSvcName
snmpbulkwalk -d -v2c -cr10 localhost hrSwInstalledName 2>&1 | grep Received | wc -l # Stuurt bulk request
snmpbulkget -d -v2c localhost hrMemorySize domPrimaryDomain -Cn2 -Cr5
snmpset beelzebub sysContact.0 s emailhere # Zet de waarde op
```
## Voorbeelden
### GetRequest
30 28 02 01 00 04 06 70 75 62 60 69 63 A0 1B 02 02 40 AD 02 01 00 02 01 00 30 0F 30 0D 06 09 2B 06 01 02 01 19 02 02 00 05 00

    30 28 SEQUENCE Length 28
    	02 01 00 Integer Length 1 0 == version
    	04 06 70 75 62 60 69 63 Octet String Length 6 public == community
    	A0 1B GetRequest Length 27
    		02 02 40 AD Integer Length 2 (64, 173)
    		02 01 00 err status
    		02 01 00 err idx
    		30 0F SEQUENCE length 15
    			30 0D SEQUENCE Length 13
    				06 09 2B 09 01 02 01 19 02 02 00 OID Length 9 "1.3.9.1.2.1.25.2.2.0"
    				05 00 Antwoord Container

### GetResponse

30 28 02 01 00 04 06 70 75 62 60 69 63 A2 1B 02 02 40 AD 02 01 00 02 01 00 30 12 30 10 06 09 2B 06 01 02 01 19 02 02 00 02 03 03 FD B8

    30 28 SEQUENCE Length 28
    	02 01 00 Integer Length 1 0 == version
    	04 06 70 75 62 60 69 63 Octet String Length 6 public == community
    	A2 1B GetResponse Length 27
    		02 02 40 AD Integer Length 2 (64, 173)
    		02 01 00 err status
    		02 01 00 err idx
    		30 12 SEQUENCE length 18
    			30 10 SEQUENCE Length 16
    				06 09 2B 09 01 02 01 19 02 02 00 OID Length 9 "1.3.9.1.2.1.25.2.2.0"
    				02 03 03 FD B8 Integer Length 3 "3,253,184"
