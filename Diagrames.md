#Diagrama de temps

```mermaid
sequenceDiagram
    participant Arduino
    participant LED
    participant Sèrie

    rect rgb(240, 240, 240)
        Arduino->>Arduino: setup()
    end

    loop Cada 2 segons
        rect rgb(224, 224, 224)
            Arduino->>LED: digitalWrite(HIGH)
            Arduino->>Sèrie: Serial.println("ENCÈS")
            bucle DURADA mil·lisegons
                Arduino->>Arduino: delay(DURADA)
            end
            Arduino->>LED: digitalWrite(LOW)
            Arduino->>Sèrie: Serial.println("APAGAT")
            bucle DURADA mil·lisegons
                Arduino->>Arduino: delay(DURADA)
            end
        end
    end

```

#

