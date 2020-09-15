 public partial class SignalGenerator : ServiceBase
    {
        private string urlHub = "http://localhost:8100/signalrhub";
        HubConnection hubConnection;

        public SignalGenerator()
        {
            InitializeComponent();

            hubConnection = new HubConnectionBuilder()
                            .WithUrl(urlHub)
                            .Build();

            hubConnection.Closed += OnClosed;
        }

        protected override void OnStart(string[] args)
        {
            StartConnection();
        }

        private async Task StartConnection()
        {
            try
            {
                await hubConnection.StartAsync();
                SendData();
            }
            catch (Exception)
            {
                await Task.Delay(2000);
                StartConnection();
            }
        }

        private async Task SendData()
        {
            Channel<ProcessData> channel = Channel.CreateBounded<ProcessData>(10);

            await hubConnection.SendAsync("DataBroadcasting", channel.Reader);

            while (await channel.Writer.WaitToWriteAsync())
            {
                while (channel.Writer.TryWrite(GenerateDataSample()))
                {
                    await Task.Delay(1000);
                }
            }

            channel.Writer.Complete();
        }

        private ProcessData GenerateDataSample()
        {
            DateTime time;
            Random random = new Random();
            int value1;
            int value2;
            int value3 = 0;
            int high = 0;
            int low = 0;

            time = DateTime.Now;
            value1 = (int)(Math.Sin(time.Second / 0.5) * Math.Sinh(time.Second / 9.55) * 200 + 30000);
            value2 = (int)(Math.Cos(time.Second / 0.5) * Math.Cos(time.Second / 9.55) * 20000 + 30000);

            if (high > 0)
            {
                value3 = 5000;
                high--;
            }
            else if (low > 0)
            {
                value3 = -5000;
                low--;
            }
            else
            {
                high = random.Next(1, 9);
                low = 10 - high;
            }

            return new ProcessData(time, value1, value2, value3);
        }
        

        private Task OnClosed(Exception ex)
        {
            StartConnection();
            return Task.CompletedTask;
        }
    }
