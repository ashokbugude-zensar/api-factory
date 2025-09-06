# GENERATION PROMPT

You will generate a valid `package.json` file based on the contents of a provided `flows.json` file.

## TASK
- Read the `flows.json` file
- Identify all non-core Node-RED node types used
- Map each node type to its corresponding contrib module
- Generate a `package.json` file that includes all required modules in the `dependencies` section with the latest compatible version from the npm registry for Node-RED v4.1.0

## RULES
- Do not include core Node-RED nodes (e.g., `http in`, `function`, `inject`, `http response`)
- Include only contrib modules required to support the flow
- Use known module names from npm (e.g., `node-red-contrib-redis` for `redis-command`)
- Output must be a valid JSON object
- Do not include any extra commentary or markdown formatting
- Do not generate packages dpendent on ajv

## OUTPUT
Return only the following file:
- `package.json`