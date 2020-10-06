# LaraBash
A ~/.bash_profile function to automatically create a Laravel application. Here's what it can do for you right now:

- Creates a database inside MAMP/MAMP Pro for the application
- Creates the initial git commit
- Modifies the .env file for the default username, password and database name 
- Opens the project inside PHP Storm (this could be modified for Atom or Sublime etc.)
- Opens `$project.dev` where `$project` is your project name (eg. laravelproject.dev). You'll need to make a new host reference in MAMP Pro for this to work.

I'll be modifying this soon for various other combinations including a simplified version for using the built in server on Mac / Laravel Valet.

##Usage
In your terminal you need to do the following `$ nano ~/.bash_profile`. When this opens, add the function below:

    function larabash {
        local project="$1"
    
        echo "Magically creating a new Laravel project: $project..."
    
        cd /Applications/MAMP/htdocs || return 1    
        composer create-project --prefer-dist laravel/laravel "$project"
        cd "$project" || return 1
    
        git init
        git add .
        git commit -m "Install Laravel"
    
        sed -e 's/DB_DATABASE=homestead/DB_DATABASE='"$project"'/g' \
            -e 's/DB_USERNAME=homestead/DB_USERNAME=root/g' \
            -e 's/DB_PASSWORD=secret/DB_PASSWORD=/g' .env >.env-new &&
        mv -f .env-new .env
    
        /Applications/MAMP/Library/bin/mysql \
            --host=localhost -uroot -proot <<SQL_END
    CREATE DATABASE \`$project\`
    DEFAULT CHARACTER SET utf8
    DEFAULT COLLATE utf8_general_ci
    SQL_END
    
        open "http://$project.dev"
        pstorm .
    
        echo "Project: $project has been created. Have fun!"
    }
    
When you've done that, make sure you `ctrl+x` followed by `y` to exit and save. Once you're out of `~/.bash_profile` you will need to do `$ source ~/.bash_profile` to make your changes active.

You'll need to make sure that MAMP/MAMP Pro is running before using this script.

Hopefully if you've followed the above correctly, when you run something like `$ larabash newProject` it should create a shiney new application for you :)
