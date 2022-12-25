# .Net MVC Yol Haritası

1. Proje için bir isim bul ve domain hakkını sorgula

2. VS .net7 projesi aç

3. Veritabanı için gerekli kütüphaneleri yükle
    >NuGet Package Manager / Manage NuGet Packages For Solution
    ```
    Microsoft.EntityFrameworkCore
    Microsoft.EntityFrameworkCore.SqlServer
    Microsoft.EntityFrameworkCore.Tools
    ```

4. Güzel bir template bulup giydir
    ```
    * Templatei wwwroot klasörünün içine kopyala

    * Templatein index.html dosyasındaki kodları kendi projenin index.cshtml dosyasına kopyala

    * /assets/ yolunu kendi dosyandaki yerine göre değiştir
        - /assets/  -->  ~/templateAdi/assets/
    ```

5. /Shared/_ViewStart.cshtml dosyasının içindekileri sil

6. Giydirdiğin templatei parçala
    ```
    * /Shared klasörünün içindeki _Layout.cshtml dosyasına tüm projede ortak kullanılacak header, sidebar, footer gibi kodları kopyala

    * Ana içerik olan çevre birimlerin ortasında kalan main content index.cshtml dosyasında kalabilir

    * _Layout dosyasına attığın kodlardaki tüm parçaları (header, sidebar, footer, scriptler, stiller vb.) yine /Shared kolasörünün içinde ayrı ayrı partial view yap ve _Layout dosyasına bu partial viewları, olması gereken yere, yerleştir

        - <partial name="_Stiller" />
        - <partial name="_Header" />        gibi    
    ```

7. _Layout.cshtml dosyasının içine ana içeriğin gelmesi gereken yere index.cshtml dosyasını yerleştir
    ```
    @RenderBody()
    ```

8. index.cshtml dosyasının içine en üste _Layout.cshtml dosyasını yerleştir
    ```
    @{
    Layout = "_Layout";
    }
    ```

9. *DBContext* classını yapılandır
    >proje/Data/projeDBContext.cs
    ```
    using proje.Models;
    using Microsoft.EntityFrameworkCore;

    namespace proje.Data
    {
    public class projeDBContext : DbContext
        {
            public projeDBContext(DbContextOptions<projeDBContext> options) : base(options)
            {
            }

            public DbSet<ModelClass1> ModelClass1ler { get; set; }
            public DbSet<ModelClass2> ModelClass2ler { get; set; }

            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder.Entity<ModelClass1>().HasData(BaşlangıçVerisi);
            }

            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder.Entity<ModelClass2>().HasData(BaşlangıçVerisi);
            }
        }
    }
    ```

10. Veritabanı için gerekli ayarlamaları yap
    >program.cs dosyası içine aşağıdaki kodları ekle
    ```
    using proje.Data;
    using Microsoft.EntityFrameworkCore;
    ```
    var builder = WebApplication.CreateBuilder(args);  --> Burası dosyanın içinde var, altına ve üstüne ekleme yapılacak
    ```
    // Add services to the container.
    var connectionString = builder.Configuration.GetConnectionString("conn") ?? throw new InvalidOperationException("'conn' bağlantısı bulunamadı.");
    builder.Services.AddDbContext<projeDBContext>(options => options.UseSqlServer(connectionString));
    ```
    >appsettings.json dosyasının içinde kodaların en üstüne aşağıdaki kodları ekle
    ```
    "ConnectionStrings": {"conn": "Server=(localdb)\\mssqllocaldb;Database=projeDB;Trusted_Connection=True;MultipleActiveResultSets=true"},
    ```

11. Gerekli model için bir class oluştur
    >proje/Models/Modelİsmi.cs

12. Oluşturduğun modelin özelliklerini belirle
    ```
    using System.ComponentModel.DataAnnotations;

    namespace proje.Models
    {
        public class Modelİsmi
        {
            [Key]
            public int/string/bool ModelÖzelliği1 { get; set; }
            public int/string/bool ModelÖzelliği2 { get; set; }
            public int/string/bool ModelÖzelliği3 { get; set; }
            public int/string/bool ModelÖzelliği4 { get; set; }
            public int/string/bool ModelÖzelliği5 { get; set; }            
        }
    }
    ```
13. Bu model için başlangıç verisi oluştur ve veritabanına ekle
    >projeDBContext.cs dosyasının içine aşağıdaki kodlar ve veriler ekelenecek
    ```
    protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Modelİsmi>().HasData(

                new Modelİsmi { ModelÖzelliği1 = özellik, ModelÖzelliği2 = özellik, ModelÖzelliği3 = özellik, ModelÖzelliği4 = özellik, ModelÖzelliği5 = özellik,},
                new Modelİsmi { ModelÖzelliği1 = özellik, ModelÖzelliği2 = özellik, ModelÖzelliği3 = özellik, ModelÖzelliği4 = özellik, ModelÖzelliği5 = özellik,},
                new Modelİsmi { ModelÖzelliği1 = özellik, ModelÖzelliği2 = özellik, ModelÖzelliği3 = özellik, ModelÖzelliği4 = özellik, ModelÖzelliği5 = özellik,},
                new Modelİsmi { ModelÖzelliği1 = özellik, ModelÖzelliği2 = özellik, ModelÖzelliği3 = özellik, ModelÖzelliği4 = özellik, ModelÖzelliği5 = özellik,},
                new Modelİsmi { ModelÖzelliği1 = özellik, ModelÖzelliği2 = özellik, ModelÖzelliği3 = özellik, ModelÖzelliği4 = özellik, ModelÖzelliği5 = özellik,}

                );
        }
    ```
14. Bu model için controller ve taskları oluştur
    >Controllers klasör sağ tık menüsünden Add/Controller

    >Açılan pencereden MVC Controller with views, using Entity Framework

    >Açılan pencereden ilgili class seçip dosya ismini ClassİsmiController olarak ayarla

    >Kendiliğinden; hem ilgili controller açılacak, hem Create, Delete, Details, Edit, Index taskları oluşacak, hem de bu taskların ilgili view sayfaları oluşacak.

    >Ayrıca ModelİsmiController.cs dosyasının içinde DI (Dependency Injection) kodları da oluşacak.
    DI (Dependency Injection) veri işlemlerini harddiskte değil ramde, yani daha hızlı yapmak için kullnılır. Böylelikle zaman ve hafıza kaybı minimuma indirilip hız arttırılmış olur.
    ```
    private readonly projeDBContext _context;

        public ModelİsmiController(projeDBContext context)
        {
            _context = context;
        }
    ```
    >Bu kodu HomeController.cs dosyasının içine de uygun şekilde yerleştir
    ```
    private readonly projeDBContext _context;

        public HomeController(projeDBContext context)
        {
            _context = context;
        }
    ```
15. Tasklar için sayfaları oluştur