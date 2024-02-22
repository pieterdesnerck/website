Add square images of committee members to this directory, 120x120px at minimum

Name format: [first year]-[last year|"current"] - [full name] - [position held].[jpg|png]

to resize images (for developers with local instance):

apk add imagemagick
magick mogrify -resize 120x120 -quality 100 -format jpg *
