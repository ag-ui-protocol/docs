# ElizaDockerfile
# Base image with Node.js
FROM node:18-slim

# Create app directory
WORKDIR /usr/src/app

# Install dependencies first (for better layer caching)
RUN npm init -y && \
    npm install telnetlib elizabot && \
    npm install --save-dev typescript ts-node @types/node @types/elizabot @types/telnetlib

# Create src directory
RUN mkdir -p src

# Create server.ts file inline
RUN echo '// server.ts - Eliza Telnet server\n\
import * as telnetlib from "telnetlib";\n\
import ElizaBot from "elizabot";\n\
\n\
function startServer() {\n\
  const server = telnetlib.createServer({}, (client: any) => {\n\
    const eliza = new ElizaBot();\n\
\n\
    // Show a welcome banner\n\
    client.on("negotiated", () => {\n\
      client.write("=====================================================\\n");\n\
      client.write("ELIZA - The first chatbot (1966) by Joseph Weizenbaum\\n");\n\
      client.write("Running via Telnet - Type '\''quit'\'' to exit\\n");\n\
      client.write("=====================================================\\n\\n");\n\
      client.write("Hello, I'\''m Eliza. How can I help you today?\\n> ");\n\
    });\n\
\n\
    // Process user input\n\
    client.on("data", (data: Buffer) => {\n\
      const userInput = data.toString().trim();\n\
\n\
      // Handle quit command\n\
      if (userInput.toLowerCase() === "quit") {\n\
        client.write("Goodbye. It was nice talking to you!\\n");\n\
        client.end();\n\
        return;\n\
      }\n\
\n\
      // Get Eliza'\''s response\n\
      const response = eliza.transform(userInput);\n\
\n\
      // Send response back to the client\n\
      client.write(response + "\\n> ");\n\
    });\n\
\n\
    // Log when clients disconnect\n\
    client.on("end", () => {\n\
      console.log("Client disconnected");\n\
    });\n\
  });\n\
\n\
  // Listen on all interfaces (important for Docker)\n\
  const PORT = 2323;\n\
  server.listen(PORT, "0.0.0.0", () => {\n\
    console.log(`Eliza Telnet server started on port ${PORT}`);\n\
    console.log(`Connect using: telnet <host-ip> ${PORT}`);\n\
  });\n\
\n\
  return server;\n\
}\n\
\n\
// Start the server when this file is run directly\n\
startServer();\n' > src/server.ts

# Create a tsconfig.json file
RUN echo '{ \
  "compilerOptions": { \
    "target": "ES2020", \
    "module": "CommonJS", \
    "strict": true, \
    "esModuleInterop": true, \
    "skipLibCheck": true, \
    "outDir": "dist" \
  }, \
  "include": ["src"] \
}' > tsconfig.json

# Expose the Telnet port
EXPOSE 2323

# Command to run the server
CMD ["npx", "ts-node", "src/server.ts"] 