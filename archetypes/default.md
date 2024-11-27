+++
date = '{{ .Date | time.Format ":date_medium" }}'
publishDate = {{ .PublishDate | time.Format ":date_medium" }} 
draft = false
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
+++
