---
title: TCP 激活
ms.date: 03/30/2017
ms.assetid: bf8c215c-0228-4f4f-85c2-e33794ec09a7
ms.openlocfilehash: e6f40b31656746d3db37545709c4e4813a7422cf
ms.sourcegitcommit: 27a15a55019f6b5f2733961738babe94aec0def3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90555180"
---
# <a name="tcp-activation"></a>TCP 激活

本示例演示承载一个服务，该服务使用 Windows 进程激活服务 (WAS) 的服务来激活通过 net.tcp 协议通信的服务。 此示例基于 [入门](getting-started-sample.md)。

> [!NOTE]
> 本主题的最后介绍了此示例的设置过程和生成说明。

> [!IMPORTANT]
> 您的计算机上可能已安装这些示例。 在继续操作之前，请先检查以下（默认）目录：
>
> `<InstallDrive>:\WF_WCF_Samples`
>
> 如果此目录不存在，请参阅[Windows Communication Foundation (wcf) ，并 Windows Workflow Foundation (的 WF](https://www.microsoft.com/download/details.aspx?id=21459)) .NET Framework Windows Communication Foundation ([!INCLUDE[wf1](../../../../includes/wf1-md.md)] 此示例位于以下目录：
>
> `<InstallDrive>:\WF_WCF_Samples\WCF\Basic\Services\Hosting\WASHost\TCPActivation`

本示例由客户端控制台程序 (.exe) 和用 WAS 激活的工作进程中承载的服务库 (.dll) 组成。 客户端活动显示在控制台窗口中。

该服务实现定义“请求-答复”通信模式的协定。 该协定由 `ICalculator` 接口定义，该接口公开数学运算（加、减、乘和除），如下面的示例代码所示：

```csharp
[ServiceContract(Namespace="http://Microsoft.ServiceModel.Samples")]
public interface ICalculator
{
    [OperationContract]
    double Add(double n1, double n2);
    [OperationContract]
    double Subtract(double n1, double n2);
    [OperationContract]
    double Multiply(double n1, double n2);
    [OperationContract]
    double Divide(double n1, double n2);
}
```

服务实现计算并返回适当的结果：

```csharp
// Service class that implements the service contract.
public class CalculatorService : ICalculator
{
    public double Add(double n1, double n2)
    {
        return n1 + n2;
    }
    public double Subtract(double n1, double n2)
    {
        return n1 - n2;
    }
    public double Multiply(double n1, double n2)
    {
        return n1 * n2;
    }
    public double Divide(double n1, double n2)
    {
        return n1 / n2;
    }
}
```

本示例使用 net.tcp 绑定的变体，该绑定启用 TCP 端口共享并关闭安全。 如果您想使用安全的 TCP 绑定，请将服务器的安全模式更改为所需的设置，并在客户端上重新运行 Svcutil.exe 以生成更新客户端配置文件。

下面的示例显示服务的配置：

```xml
<system.serviceModel>

    <services>
      <service name="Microsoft.ServiceModel.Samples.CalculatorService"
               behaviorConfiguration="CalculatorServiceBehavior">
        <!-- This endpoint is exposed at the base address provided by host: net.tcp://localhost/servicemodelsamples/service.svc  -->
        <endpoint binding="netTcpBinding" bindingConfiguration="PortSharingBinding"
          contract="Microsoft.ServiceModel.Samples.ICalculator" />
        <!-- the mex endpoint is exposed at net.tcp://localhost/servicemodelsamples/service.svc/mex -->
        <endpoint address="mex"
                  binding="mexTcpBinding"
                  contract="IMetadataExchange" />
      </service>
    </services>
    <bindings>
      <netTcpBinding>
        <binding name="PortSharingBinding" portSharingEnabled="true">
          <security mode="None" />
        </binding>
      </netTcpBinding>
    </bindings>

    <!--For debugging purposes set the includeExceptionDetailInFaults attribute to true-->
    <behaviors>
      <serviceBehaviors>
        <behavior name="CalculatorServiceBehavior">
          <serviceMetadata />
          <serviceDebug includeExceptionDetailInFaults="False" />
        </behavior>
      </serviceBehaviors>
    </behaviors>

  </system.serviceModel>
```

客户端的终结点按如下所示的示例代码进行配置：

```xml
<system.serviceModel>
    <bindings>
        <netTcpBinding>
          <binding name="NetTcpBinding_ICalculator">
            <security mode="None"/>
          </binding>
        </netTcpBinding>
    </bindings>
    <client>
        <endpoint address="net.tcp://localhost/servicemodelsamples/service.svc"
            binding="netTcpBinding" bindingConfiguration="NetTcpBinding_ICalculator"
            contract="Microsoft.ServiceModel.Samples.ICalculator" name="NetTcpBinding_ICalculator" />
    </client>
</system.serviceModel>
```

运行示例时，操作请求和响应将显示在客户端控制台窗口中。 在客户端窗口中按 Enter 可以关闭客户端。

```console
Add(100,15.99) = 115.99
Subtract(145,76.54) = 68.46
Multiply(9,81.25) = 731.25
Divide(22,7) = 3.14285714285714

Press <ENTER> to terminate client.
```

### <a name="to-set-up-build-and-run-the-sample"></a>设置、生成和运行示例

1. 确保已安装 IIS 7.0。 激活 WAS 需要 IIS 7.0。

2. 请确保已 [为 Windows Communication Foundation 示例执行了一次性安装过程](one-time-setup-procedure-for-the-wcf-samples.md)。

    此外，还必须安装 WCF 非 HTTP 激活组件：

    1. 从“开始”菜单中，选择“控制面板” 。

    2. 选择 " **程序和功能**"。

    3. 单击 **"打开或关闭 Windows 组件"**。

    4. 展开 **Microsoft .NET Framework 3.0** 节点并检查 **Windows Communication Foundation 非 HTTP 激活** 功能。

3. 配置 WAS 以支持 TCP 激活。

    为方便起见，在位于示例目录中名为 AddNetTcpSiteBinding.cmd 的批处理文件中实现以下两个步骤。

    1. 若要支持 net.tcp 激活，必须首先将默认的网站绑定到一个 net.tcp 端口。 可以使用随 Internet 信息服务 7.0 (IIS) 管理工具集一起安装的 Appcmd.exe 来完成此操作。 在管理员级别命令提示符处，运行以下命令：

        ```console
        %windir%\system32\inetsrv\appcmd.exe set site "Default Web Site" -+bindings.[protocol='net.tcp',bindingInformation='808:*']
        ```

        > [!TIP]
        > 此命令是单行文本。 此命令将 net.tcp 网站绑定添加到以任何主机名侦听 TCP 端口 808 的默认网站。

    2. 尽管网站内的所有应用程序共享一个公共 net.tcp 绑定，但是每个应用程序可以单独启用 net.tcp 支持。 若要启用 /servicemodelsamples 应用程序的 net.tcp，请在管理员级别命令提示符处运行以下命令：

        ```console
        %windir%\system32\inetsrv\appcmd.exe set app
        "Default Web Site/servicemodelsamples" /enabledProtocols:http,net.tcp
        ```

        > [!NOTE]
        > 此命令是单行文本。 此命令允许使用和访问/servicemodelsamples 应用程序 `http://localhost/servicemodelsamples` `net.tcp://localhost/servicemodelsamples` 。

4. 若要生成 C# 或 Visual Basic .NET 版本的解决方案，请按照 [Building the Windows Communication Foundation Samples](building-the-samples.md)中的说明进行操作。

5. 若要以单机配置或跨计算机配置来运行示例，请按照 [运行 Windows Communication Foundation 示例](running-the-samples.md)中的说明进行操作。

    移除为此示例添加的 net.tcp 网站绑定。

    为方便起见，在位于示例目录中名为 RemoveNetTcpSiteBinding.cmd 的批处理文件中实现以下两个步骤。

    1. 通过在管理员级别命令提示符处运行以下命令，可从启用的协议列表移除 net.tcp：

        ```console
        %windir%\system32\inetsrv\appcmd.exe set app
        "Default Web Site/servicemodelsamples" /enabledProtocols:http
        ```

        > [!NOTE]
        > 必须以单行文本的形式输入此命令。

    2. 通过在管理员级别命令提示符处运行以下命令，可移除 net.tcp 站点绑定：

        ```console
        %windir%\system32\inetsrv\appcmd.exe set site "Default Web Site"
        --bindings.[protocol='net.tcp',bindingInformation='808:*']
        ```

        > [!NOTE]
        > 必须以单行文本的形式键入此命令。

## <a name="see-also"></a>请参阅

- [AppFabric 承载和持久性示例](/previous-versions/appfabric/ff383418(v=azure.10))
