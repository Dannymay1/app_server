apt install npm
npm install -g create-react-app@3.4.1
npm init react-app webserver --use-npm
npm install react-scripts@3.4.1 -g --silent
cd into the webserver folder
cat > src/App.js <<EOF
import logo from './logo.svg';
import './App.css';
function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
             Add any content for your website << change this line (remove this line and create you own content)
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
EOF
npm run build
create a Dockerfile named Dockerfile.prod by running the command below
cat > Dockerfile.prod <<EOF
FROM node:13.12.0-alpine as build
WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH
COPY package.json ./
COPY package-lock.json ./
COPY . ./
FROM nginx:stable-alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

EOF
use the command below to create a docker-compose file for the deployment
cat > docker-compose.yaml <<EOF
version: '3.7'

services:
  webserver-prod:
    container_name: webserver-prod
    build:
      context: .
      dockerfile: Dockerfile.prod
    ports:
      - '80:80'

EOF
docker-compose -f docker-compose.yaml up -d --build



cat > src/App.js <<EOF
import logo from './logo.svg';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
             Add any content for your website
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
EOF

