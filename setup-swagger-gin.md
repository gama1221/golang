# How to setup swagger documentation for gin
## Step 1: Install Required Packages
First, you need to install the required dependencies.
- **Install swag (Swagger generator) using go install:**
```sh
go install github.com/swaggo/swag/cmd/swag@latest
```
- **Install the gin-swagger middleware for Gin:**
```sh
go get github.com/gin-gonic/gin
go get github.com/swaggo/gin-swagger
go get github.com/swaggo/swag
```
## Step 2: Add Swagger Documentation to Your API
Update your server.go/main.go to add Swagger comments and serve the Swagger documentation.
```go
package main

import (
	"fmt"
	"net/http"

	"github.com/gama1221/gama-crypto-exchange/controllers"
	"github.com/gama1221/gama-crypto-exchange/docs" // This will import the generated docs package
	"github.com/gama1221/gama-crypto-exchange/services"
	"github.com/gin-gonic/gin" // Correct import for swaggerFiles
	swaggerFiles "github.com/swaggo/files"
	ginSwagger "github.com/swaggo/gin-swagger"
)

// @title Gama Crypto Exchange API
// @version 1.0
// @description This is a sample server for the Gama Crypto Exchange API using Gin and Swagger.
// @termsOfService http://swagger.io/terms/

// @contact.name API Support
// @contact.url http://www.swagger.io/support
// @contact.email support@swagger.io

// @license.name MIT
// @license.url https://opensource.org/licenses/MIT

// @host localhost:5050
// @BasePath /
var (
	userService    services.UserService       = services.New()
	userController controllers.UserController = controllers.New(userService)
)

func main() {
	// Setup Gin router
	router := gin.Default()

	// Initialize Swagger docs
	docs.SwaggerInfo.BasePath = "/"

	router.GET("/users", GetUsers)
	router.POST("/users", createUser)

	url := ginSwagger.URL("http://localhost:5050/swagger/doc.json")

	// Serve Swagger docs at /swagger/index.html
	router.GET("/swagger/*any", ginSwagger.WrapHandler(swaggerFiles.Handler, url))
	err := router.Run("5050")
	if err != nil {
		fmt.Println(err)
	}
	// Start server
	router.Run(":5050")
}

// @Summary Get all users
// @Description Retrieve all users data from the server
// @Tags users
// @Accept  json
// @Produce  json
// @Success 200 {array} entities.User "Successfully retrieved all users"
// @Router /users [get]
func GetUsers(c *gin.Context) {
	users := userController.FindAll()
	c.JSON(http.StatusOK, users)
}

// @Summary Create a new user
// @Description Add a new user to the server
// @Tags users
// @Accept  json
// @Produce  json
// @Param user body entities.User true "User object that needs to be created"
// @Success 200 {object} entities.User "Successfully created user"
// @Router /users [post]
func createUser(c *gin.Context) {
	user := userController.Save(c)
	c.JSON(http.StatusOK, user)
}
```
