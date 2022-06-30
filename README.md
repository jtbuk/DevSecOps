# DevSecOps in an Azure / Azure DevOps Environment

DevSecOps is the process of empowering software developers to do security work.

https://github.com/6mile/DevSecOps-Playbook Offers a good starting reference.

# Some of the highest value, low effort tasks are:

- Using source control, and commiting infrastructure as code

- Implementing a code review process, and sensible branch policies such as
  - Minimum number of reviewers
  - Build validation
    - Unit / Integration Tests
    - Linting    
    - Automatically assign reviewers for certain types of files e.g. Devops expert for .config changes.
    - Azure DevOps [PR based code coverage](https://docs.microsoft.com/en-us/azure/devops/pipelines/test/codecoverage-for-pullrequests?view=azure-devops)
      ```
      - task: DotNetCoreCLI@2
        displayName: 'Tests'
        inputs:
          command: test
          projects: 'MyProject.csproj'
          arguments: '--configuration debug --collect:"Code coverage" --settings .runsettings'
          publishTestResults: true
          testRunTitle: "Run Tests"
      ```

- Spending the time to actually configure Azure DevOps Permissions

- Static Analysis tools such as
  - [SonarAnalyzer](https://www.nuget.org/packages/SonarAnalyzer.CSharp/)
  - [SecurityCodeScan](https://www.nuget.org/packages/SecurityCodeScan.VS2019/)
  - If on NET 6+ a decent configuration for the built in Analysers:
    ```
    <PropertyGroup>
      <AnalysisMode>All</AnalysisMode>
    </PropertyGroup>
    ```
    This is configurable e.g.
    ```
    <PropertyGroup>
      <AnalysisMode>Recommended</AnalysisMode>
      <AnalysisModeGlobalization>None</AnalysisModeGlobalization>
      <AnalysisModeSecurity>All</AnalysisModeSecurity>
    </PropertyGroup>
    ``` 
  
- A scheduled pipeline for scanning for vulnerabilities in third party libraries obtained via NPM / NuGet / Docker etc.
  - ```npm audit --audit-level="high" --production```
  - ```dotnet list package --vulnerable --source https://api.nuget.org/v3/index.json```
  
- Dynamic Application Security Tool (DAST) such as [Detectify](https://detectify.com/). Detectify is pretty much point and click, it costs £50 per month currently per scan profile. Which you can scan multiple apps with if they're on the same domain.

- Azure App Insights monitoring and alerts, if you're going fancy, use an Azure logic app to push your alerts to a slack workflow.

- A decent Web Application Firewall (WAF) using the [ModSecurity Core Rule Set](https://owasp.org/www-project-modsecurity-core-rule-set/)

- Ideally have a DEV / STAGING / PROD environment using a CI/CD process to deploy

- Use Feature flags, and commit often. If something is critically broken you can switch the feature off.

- Azure KeyVault or similar credential store for sensitive data

- Encrypt all storage, Encrypt all traffic
