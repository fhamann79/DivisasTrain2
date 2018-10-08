Divisas MVVM - #NoCodeBehind

1.	Cree un proyecto Xamarin Forms, llamado Divisas y asegúrese que el “Welcome to Xamarin forms” le esté funcionando en las 3 plataformas:
Android 
 	iOS
 	UWP
 

2.	Elimina el archivo MainPage.

3.	Vamos a modificar nuestra App.xaml para crear el diccionario de recursos, inicialmente con los colores que va a manejar nuestra aplicación, debe quedar así:
<?xml version="1.0" encoding="utf-8" ?>
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DivisasMVVM.App">
  <Application.Resources>
    <ResourceDictionary>
    </ResourceDictionary>
  </Application.Resources>
</Application>
4.	Vamos a crear la carpeta Pages y dentro de esta vamos a crear el MainPage:
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DivisasMVVM.Pages.MainPage"
             Title="Divisas">
  <Label Text="Main Page" VerticalOptions="Center" HorizontalOptions="Center" />
</ContentPage>
5.	Cambiamos el App.xaml.cs por:
public App()
{
   InitializeComponent();
   MainPage = new NavigationPage(new MainPage());
}
Y probamos que nuestro proyecto aun funcione:
Android 
 	iOS
 	UWP
 

6.	Creamos la carpeta ViewModels y dentro de esta vamos a crear el MainViewModel, por el momento vacía:
public class MainViewModel
{
}

7.	Crearemos esta clase para poder implementar el patrón Locator. Para tal fin vamos a crear la carpeta Infrastructure y dentro de esta la clase InstanceLocator
public class InstanceLocator
{
    public MainViewModel Main { get; set; }

    public InstanceLocator()
    {
        Main = new MainViewModel();
    }
}
8.	Vamos hacer que nuestro InstanceLocator sea un recurso general para toda la aplicación. Vamos al App.xaml y le hacemos las siguientes modificaciones:
<?xml version="1.0" encoding="utf-8" ?>
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:infra="clr-namespace:DivisasMVVM.Infrastructure;assembly=DivisasMVVM"
             x:Class="DivisasMVVM.App">
  <Application.Resources>
    <ResourceDictionary>
      <!-- Locator -->
      <infra:InstanceLocator x:Key="Locator"></infra:InstanceLocator>
    </ResourceDictionary>
  </Application.Resources>
</Application>
9.	Ahora hagamos que nuestra MainPage, use el MainViewModel, y modificamos esta página para que muestre nuestra página como la necesitamos:
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DivisasMVVM.Pages.MainPage"
             Title="Divisas"
             BindingContext="{Binding Main, Source={StaticResource Locator}}">
<ScrollView>
        <StackLayout
            Padding="20">
            <Label
                Text="Dólares">
                
            </Label>
            <Entry
                Placeholder="Ingrese la cantidad en dólares"
                Keyboard="Numeric">
                
            </Entry>
            <Button
                
                Text="Convertir">
                
            </Button>

            <Label
                Text="Pesos">

            </Label>
            <Entry
                
                Placeholder="Cantidad en pesos colombianos"
                IsEnabled="False">

            </Entry>
            
            <Label
                Text="Euros">

            </Label>
            <Entry
                
                Placeholder="Cantidad en Euros"
                IsEnabled="False">

            </Entry>
            
            <Label
                Text="Libras">

            </Label>
            <Entry
                
                Placeholder="Cantidad en Libras"
                IsEnabled="False">

            </Entry>
        </StackLayout>
    </ScrollView>
</ContentPage>
 
10.	Probemos como queda nuestra interfaz de usuario:
Android 
 	iOS
 	UWP
 
11.	Por buenas prácticas creemos la carpeta Services y dentro de esta el DialogService, que vamos a usar más adelante:
public class DialogService
{
    public async Task ShowMessage(string title, string message)
    {
        await App.Current.MainPage.DisplayAlert(title, message, "Aceptar");
    }

    public async Task<bool> ShowConfirm(string title, string message)
    {
        return await App.Current.MainPage.DisplayAlert(title, message, "Sí", "No");
    }
}

12.	Modifiquemos nuestro MainPage para que los controles se “binden” al MainViewModel:
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DivisasMVVM.Pages.MainPage"
             Title="Divisas"
             BindingContext="{Binding Main, Source={StaticResource Locator}}">
  <ScrollView>
        <StackLayout
            Padding="20">
            <Label
                Text="Dólares">
                
            </Label>
            <Entry
                Text="{Binding Dollars}"
                Placeholder="Ingrese la cantidad en dólares"
                Keyboard="Numeric">
                
            </Entry>
            <Button
                Command="{Binding ConvertCommand}"
                Text="Convertir">
                
            </Button>

            <Label
                Text="Pesos">

            </Label>
            <Entry
                Text="{Binding Pesos, StringFormat='${0:N2}'}"
                Placeholder="Cantidad en pesos colombianos"
                IsEnabled="False">

            </Entry>
            
            <Label
                Text="Euros">

            </Label>
            <Entry
                Text="{Binding Euros, StringFormat='€{0:N2}'}"
                Placeholder="Cantidad en Euros"
                IsEnabled="False">

            </Entry>
            
            <Label
                Text="Libras">

            </Label>
            <Entry
                Text="{Binding Pounds, StringFormat='£{0:N2}'}"
                Placeholder="Cantidad en Libras"
                IsEnabled="False">

            </Entry>
        </StackLayout>
    </ScrollView>
</ContentPage>
13.	Y modifiquemos nuestro MainViewModel:
using Divisas.Services;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Input;
using Xamarin.Forms;

namespace Divisas.ViewModels
{
    public class MainViewModel: BindableObject 
    {
        #region Atributes

        private decimal dollars;

        private decimal pesos;

        private decimal euros;

        private decimal pounds;

        private DialogService dialogService;

        #endregion

        #region Constructor
        public MainViewModel()
        {
            dialogService = new DialogService();
        } 
        #endregion


        #region Properties
        public decimal Dollars
        {
            get
            {
                return dollars;
            }
            set
            {
                if (dollars != value)
                {
                    dollars = value;
                    OnPropertyChanged("Dollars");
                }
            }
        }

        public decimal Pesos
        {
            get
            {
                return pesos;
            }
            set
            {
                if (pesos!=value)
                {
                    pesos = value;
                    OnPropertyChanged("Pesos");
                }
            }
        }

        public decimal Pounds
        {
            get
            {
                return pounds;
            }
            set
            {
                if (pounds!= value)
                {
                    pounds = value;
                    OnPropertyChanged("Pounds");
                }
            }
        }

        public decimal Euros
        {
            get
            {
                return euros;
            }
            set
            {
                if (euros != value)
                {
                    euros = value;
                    OnPropertyChanged("Euros");
                }
            }
        }
        #endregion

        #region Commands
        public ICommand ConvertCommand { get { return new Command(ConvertMoney); } }

        private async void ConvertMoney()
        {
            if (Dollars <= 0)
            {
                await dialogService.ShowMessage("Error", "Se debe ingresar un valor en dólares mayor que 0");
                return;
            }
            Pesos = Dollars * (decimal)3039.51368;
            Pounds = Dollars * (decimal)0.789973654;
            Euros = Dollars * (decimal)0.897070169;
        } 
        #endregion
    }
}

 
14.	Probamos como queda nuestra aplicación terminada:
Android 
 

 	iOS
 

 	UWP
 



 

