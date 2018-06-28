# AP.Core.IoCManager
A flexible IocC Container  supports .Net Framework and .Net Core 2.0 Fast and Support IServiceProvider integration
# Using 
The following is the simplest example of using the IoC container
```
public interface IAnimal {  } 
public class Dog : IAnimal {  } 
public class Cow : IAnimal {  } 

public interface IAnimalManager {  } 
public class AnimalManager : IAnimalManager { 
    public AnimalManager(IEnumerable<IAnimal> animals) {  } 
}
void  Example ()  { 
    // Create a new container 
    IBaseContainer container = new BaseContainer(); 

    // Register type 
    container.Register<IAnimal,Dog>(); 
    container.Register<IAnimal,Cow>(); 
    container.Register<IAnimalManager,AnimalManager>(ReuseType.Singleton); 
    
    // animals contains two instances, one is Dog and the other is Cow 
    var animals = container.ResolveMany<IAnimal>();
    
    // animalManager's constructor will automatically pass in a list of two instances 
    var animalManager = container.Resolve<IAnimalManager>(); 

    // When the service registered as a singleton (Singleton) resolves again Use the original instance 
    // where animalManager == otherAnimalManager 
    var otherAnimalManager = container.Resolve<IAnimalManager>(); 
} 
```
# Global IoC container
In the above example, the container was created manually. You do not need to manually create the container when actually developing the program, because there is a global default in IoC. The global IoC container can be used to:
```
var someService = IoC.Container.Resolve<ISomeService>();
```

# IServiceProvider integration
The current web container implements Microsoft's DI interface and supports the replacement of containers used in Asp.Net Core.
### Registration IServiceCollectioncan be used
```
 public IServiceProvider ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            IoC.Container.RegisterSingleton<IDemoService, DemoService>();
            IoC.Container.RegisterFromServiceCollection(services);
            return IoC.Container.AsServiceProvider();
        }
```
// Get multiple times will return the same instance 
```
    var serviceProvider = IoC.Container.AsServiceProvider();
```
The Asp.Net Core project created by the current project builder will replace the default container. You do not need to manually write the above code.

# Controller Inject Service
```
public class ValuesController : ControllerBase
    {
        private readonly IDemoService _demoSrv;
        public ValuesController(IDemoService demoSrv)
        {
            this._demoSrv = demoSrv;
        }
    }
```
# Method Inject Service
```
    [HttpGet]
    public async Task<dynamic> Get()
    {
        //Inject
        var demoSrv =  IoC.Container.Resolve<IDemoService>();
        demoSrv.DoSomeThing();
        //Logic
    }
```
