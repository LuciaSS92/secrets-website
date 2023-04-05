// const md5 = require("md5");
// bcrypt npm package & require
const bcrypt = require("bcrypt");
const saltRounds = 10;

const userSchema = new mongoose.Schema({
  email: String,
  password: String,
});

const User = new mongoose.model("User", userSchema);

app.get("/", function (req, res) {
  res.render("home");
});

//REGISTER

app.get("/register", function (req, res) {
  res.render("register");
});

app.post("/register", function (req, res) {
  const hash = bcrypt.hashSync(req.body.password, saltRounds); // bcrypt
  const newUser = new User({
    email: req.body.username,

    password: hash, // bcrypt
  });
  newUser
    .save()
    .then(() => {
      res.render("secrets");
    })
    .catch((err) => {
      console.log(err);
    });
});

//LOGIN

app.get("/login", function (req, res) {
  res.render("login");
});

app.post("/login", function (req, res) {
  const username = req.body.username;
  const password = req.body.password;

  User.findOne({ email: username })
    .then((foundUser) => {
      if (foundUser) {
        // bcrypt
        if (bcrypt.compareSync(req.body.password, foundUser.password)) {
          res.render("secrets");
        }
      }
    })
    .catch((err) => {
      console.log(err);
    });
});

app.listen(3000, function () {
  console.log("Server started on port 3000");
});