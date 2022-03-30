# 2. install npm as your package manager
sudo apt install npm

# 3. install react dependencies
sudo npm install -g create-react-app@3.4.1

# 4. generate a sample react App
npm init react-app webserver --use-npm

# 5. install react script dependencies
npm install react-scripts@3.4.1 -g --silent

# 5.1 cd into the webserver folder


# 6. modify the homepage of your react app and change "Add any content for your website" to the content you want on your website

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

# 7. create the build
npm run build


# 8.create a Dockerfile named Dockerfile.prod by running the command below

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


# 9. use the command below to create a docker-compose file for the deployment

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


# 10. use the command below to deploy the containerize webserver
docker-compose -f docker-compose.yaml up -d --build



# 11.  modify the homepage of your react app again  and change "Add any content for your website" to the content you want on your website which is different from the value provide earlier to section 6

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

# 12. sync the build folder to the s3 provisioned on this project
