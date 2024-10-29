# BadCode: Gestor de notificaciones

El ejemplo gestiona notificaciones de diferentes tipos (SMS, Email, y Push). Este ejemplo puede beneficiarse del uso de patrones de diseño para manejar la creación de notificaciones y mejorar la escalabilidad y mantenibilidad del código.

### Codigo:
```
using System;

namespace NotificationManager
{
    public class NotificationService
    {
        public void SendNotification(string type, string message)
        {
            if (type == "SMS")
            {
                Console.WriteLine("Enviando SMS: " + message);
            }
            else if (type == "Email")
            {
                Console.WriteLine("Enviando Email: " + message);
            }
            else if (type == "Push")
            {
                Console.WriteLine("Enviando Notificación Push: " + message);
            }
            else
            {
                Console.WriteLine("Tipo de notificación no soportado");
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            NotificationService notificationService = new NotificationService();
            notificationService.SendNotification("SMS", "Mensaje de prueba para SMS");
            notificationService.SendNotification("Email", "Mensaje de prueba para Email");
            notificationService.SendNotification("Push", "Mensaje de prueba para Push");
            notificationService.SendNotification("Fax", "Mensaje de prueba para Fax");
        }
    }
}
```
### Problemas del codigo:
- Código rígido: Agregar un nuevo tipo de notificación requiere modificar SendNotification.
- Violación de Principios SOLID:
  - OCP (Open-Closed Principle): No es fácil extender la funcionalidad sin modificar el código existente.
  - SRP (Single Responsibility Principle): NotificationService maneja varios tipos de notificaciones y sus lógicas.

## Mejoras utilizando el Patrón Factory Method
Para mejorar este código, podemos utilizar el Factory Method para delegar la creación de diferentes tipos de notificaciones, lo que hace que el código sea más extensible y flexible​​.

```
using System;

namespace NotificationManager
{
    // Producto abstracto
    public abstract class Notification
    {
        public abstract void Send(string message);
    }

    // Productos concretos
    public class SMSNotification : Notification
    {
        public override void Send(string message)
        {
            Console.WriteLine("Enviando SMS: " + message);
        }
    }

    public class EmailNotification : Notification
    {
        public override void Send(string message)
        {
            Console.WriteLine("Enviando Email: " + message);
        }
    }

    public class PushNotification : Notification
    {
        public override void Send(string message)
        {
            Console.WriteLine("Enviando Notificación Push: " + message);
        }
    }

    // Creador abstracto
    public abstract class NotificationFactory
    {
        public abstract Notification CreateNotification();
    }

    // Creadores concretos
    public class SMSNotificationFactory : NotificationFactory
    {
        public override Notification CreateNotification()
        {
            return new SMSNotification();
        }
    }

    public class EmailNotificationFactory : NotificationFactory
    {
        public override Notification CreateNotification()
        {
            return new EmailNotification();
        }
    }

    public class PushNotificationFactory : NotificationFactory
    {
        public override Notification CreateNotification()
        {
            return new PushNotification();
        }
    }

    // Clase cliente
    public class NotificationService
    {
        private NotificationFactory _factory;

        public NotificationService(NotificationFactory factory)
        {
            _factory = factory;
        }

        public void SendNotification(string message)
        {
            var notification = _factory.CreateNotification();
            notification.Send(message);
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            NotificationService smsService = new NotificationService(new SMSNotificationFactory());
            smsService.SendNotification("Mensaje de prueba para SMS");

            NotificationService emailService = new NotificationService(new EmailNotificationFactory());
            emailService.SendNotification("Mensaje de prueba para Email");

            NotificationService pushService = new NotificationService(new PushNotificationFactory());
            pushService.SendNotification("Mensaje de prueba para Push");
        }
    }
}
```
## Mejoras logradas:
- Extensibilidad: Se pueden agregar nuevos tipos de notificaciones creando nuevas clases de productos y fábricas sin modificar NotificationService.
- Cumplimiento del Principio OCP: NotificationService está abierto para extensión y cerrado para modificación.
- Claridad y Mantenibilidad: Cada tipo de notificación tiene su propia clase, lo que hace que el código sea más fácil de mantener y leer​​.
