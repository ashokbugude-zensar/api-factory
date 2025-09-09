# INPUT
You are provided `flows.json` file.

# TASK
Analyze `flows.json` file and generate a text file that includes all required contrib modules

# OUTPUT
- A single file `package.txt` with all required dependencies along with latest compatible npm version numbers from npm registry for NODE_RED 4.1.

# TASK
- Read the `flows.json` file
- Identify all non-core Node-RED node types used
- Map each node type to its corresponding contrib module
- Generate a text file that includes all required modules, each module in a new line

Ensure that the generated text file includes:
- All non-core Node-RED node types used in `flows.json`
- Correct module names that provide those node types


# RULES
- Do not include core Node-RED nodes (e.g., `http in`, `function`, `inject`, etc.)
- Only include contrib modules that are required to support the node types used in the flow
- Use known, published module names from npm
- Output must be a text file with each module in a separate line
- Do not include any commentary or explanation in the output
- Use known module names from npm
- Do not generate packages dpendent on ajv
- Do not add default version numbers egainst packages
- Do not include default Node-RED installation packages

# OUTPUT
Return only the following file:
- `package.txt`