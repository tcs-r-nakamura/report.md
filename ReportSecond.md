# (1) MainFormにran()の定義がされていない

- **修正前**
```csharp
using System;
//外部アプリを使えるようにする
using System.Diagnostics;

  static void Main()
  {
      Application.SetHighDpiMode(HighDpiMode.SystemAware);
      Application.EnableVisualStyles();
      Application.SetCompatibleTextRenderingDefault(false);

      // --- Dependency Injection ---
      var view = new MainForm();
      var model = new ScheduleModel();
      // Save data in the same directory as the executable
      var repository = new ScheduleRepository("schedule_data_csharp.json");

      var presenter = new SchedulePresenter(view, model, repository);

      view.Run();
  }
```
- **影響範囲**
  - 

- **修正後**
```csharp
using ScheduleManager.Models;
using ScheduleManager.Repositories;
using ScheduleManager.Views;
using static ScheduleManager.Presenters.ScedulePresenter;

namespace ScheduleManager
{
    internal static class Program
    {
        /// <summary>
        ///  The main entry point for the application.
        /// </summary>
        [STAThread]
        static void Main()
        {
            Application.SetHighDpiMode(HighDpiMode.SystemAware);
            Application.EnableVisualStyles();
            Application.SetCompatibleTextRenderingDefault(false);

            // --- Dependency Injection ---
            var view = new MainForm();
            var model = new ScheduleModel();
            // Save data in the same directory as the executable
            var repository = new ScheduleRepository("schedule_data_csharp.json");

            var presenter = new SchedulePresenter(view, model, repository);

            Application.Run(view);
        }
    }
}
```

# (2)classの中にClassがありオブジェクトが呼び出せない
- **修正前**
```csharp
 internal class ScedulePresenter
 {
     /// <summary>
     /// スケジュール管理プレゼンター
     /// </summary>
     public class SchedulePresenter
     {
         private readonly IScheduleView _view;
         private readonly ScheduleModel _model;
         private readonly ScheduleRepository _repository;
     }
}
```

- **影響範囲**

- **修正後**
```csharp
  /// <summary>
  /// スケジュール管理プレゼンター
  /// </summary>
  public class SchedulePresenter
  {
      private readonly IScheduleView _view;
      private readonly ScheduleModel _model;
      private readonly ScheduleRepository _repository;
```

# (3)ファイル名の不一致
- **修正前**
- ScedulePresenter
- **修正後**
- SchedulePresenter

# (4)awaitのつけ忘れ
- **修正前**
```csharp
  private async Task LoadInitialDataAsync()
  {
      try
      {
          var schedules = _repository.LoadAsync();
          _model.SetSchedules(schedules);
          _view.DisplaySchedules(_model.GetAllSchedules());
      }
      catch (System.Exception ex)
      {
          _view.ShowMessage($"スケジュールの読み込みに失敗しました: {ex.Message}", "エラー", true);
      }
  }
```
- **修正後**
```csharp
  private async Task LoadInitialDataAsync()
  {
      try
      {
          var schedules = await _repository.LoadAsync();
          _model.SetSchedules(schedules);
          _view.DisplaySchedules(_model.GetAllSchedules());
      }
      catch (System.Exception ex)
      {
          _view.ShowMessage($"スケジュールの読み込みに失敗しました: {ex.Message}", "エラー", true);
      }
  }
```

# (5)MainFormはインターフェイスのIScheduleView.Run()を実装していない	

- **修正前**
```csharp
 public partial class MainForm : Form, IScheduleView
```
- **修正後**
```csharp
  public void Run()
  {
      Application.Run(this);
  }
```
