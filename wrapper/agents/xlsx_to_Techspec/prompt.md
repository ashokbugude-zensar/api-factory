**Task**
- Understand in-depth Technical and business requirements from the input xlsx.


**Input Format**
An xlsx file with below columns with description of each column.
1. Questions - Contatining requirements in the form of questions
2. Answers - Containing answers to the question
3. Remarks for Answer Selected - Reason why a partcular answer was selected

**Output**
- Generate a document in .md format containing Business Spec and highly detailed professionally formatted Tech Spec to be given to an LLM to implement the technical requirements and create a production-ready, enterprise, error-free, understandable code.


**Output Format**
Output should contain below sections
1. Business requirements 
    - Explain business requirements in detail technically

2. Technologies and version 
    - List the Technologies and corresponding versions

3. Project Details 
    - Project Name, Base Package name, package folder and structure, file names, file paths, architectural style, versioning folders, Project Structure, any other possible project details in key value format.

4. Controllers 
    - List and path of controller files with Endpoints, headers, methods, parameters, DTOs, validation rules, header validations, requests, responses, error codes, input validations 

5. Models 
    - List of model files

6. Services 
    - List all service files

7. DTOs 
    - List all DTOs

8. Utils 
    - List all utils to be implemented.
    - Steps to implement

9. Configs 
    - List all the config files to be created.
    - Steps to implement

8. Security
    - (authN/Z, headers, mTLS) List the library used and all implementation details, list of files to be created in config, services, utils etc
    - Any secret managers used with configuration details.
    - Steps to implement

9. Logging 
    - List the library,framework, annotations  used and all implementation details, list of files to be created in config, services, utils etc
    - Steps to implement

10. Observability 
    - List the library,framework used and all implementation details, list of files to be created in config, services, utils etc
    - Steps to implement

11. Metrics 
    - List the library,framework used and all implementation details, list of files to be created in config, services, utils etc
    - Steps to implement

12. Tracing 
    - List the library,framework used and all implementation details, list of files to be created in config, services, utils etc
    - Steps to implement

13. Resilience
    - (timeouts, retries, circuit breakers, fallbacks) List the library,framework used and all implementation details, list of files to be created in config, services, utils etc
    - Steps to implement

14. Caching
    - List the library,framework used and all implementation details, list of files to be created in config, services, utils etc
    - List Performance, rate limiting expectations
    - Steps to implement

15. Integrations
    - List all the external integrations with services which are expected to be integrated. 
    - All implementation details, list of files to be created in config, services, utils etc.
    - Files which should implement the integrations with logic.
    - Steps to implement

16. Deployment
   - List all possible Profiles from the Profiles section which the apis should support. Do not miss any profile. The profile names could be separated by comma in same line, these should also be considered.
   - Identify and List all the yaml files and environment configuration files to be listed in the 'k8s-manifests' directory from the in input
   - List environment-specific configuration and deployment targets
   - Any other required files

19. Code Formatting
    - List all the code formatting plugins,libraries,frameworks,stylesheets,xml files used 
    - Display each of them in seperate lines mentioning the type if present in input 

17. Testing
    - List all testing frameworks, tools, coverage thresholds frameworks, tools, coverage thresholds

18. Documentation
    - Provide Logic for implementation
    - Mention details about integrating different libraries, services etc.
    - Add any other missing section with detailed implementation steps

19. Implementaion
    - Mention logic/implementaion steps as a prompt which can be understood by an LLM (gpt-40-mini)
    and generate a production ready, entreprise ready, error free, understandable code.

**Notes**
- Assume any missing details suitably which is industry standard
- Connect all independent files together
- Instructions should be simple and easy to implement by LLM without hallucination
