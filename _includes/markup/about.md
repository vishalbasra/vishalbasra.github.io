<html>
<head>
<style>
{
  box-sizing: border-box;
}

.column {
  float: left;
  width: 33.33%;
  padding: 5px;
}

/* Clearfix (clear floats) */
.row::after {
  content: "";
  clear: both;
  display: table;
}
img{
  border-radius: 50%;
}
</style>
</head>
<body>

<div class="row">
  <div class="column">
    <img src="/public/images/me.jpg" alt="Snow" style="width:100%" >
  </div>

    <p>
    My name's Vishal and here, I hope to compile and put out things that I find interesting, hoping that other people might find them interesting too!
    <br>
    I play video-games and work as a <a href="https://en.wikipedia.org/wiki/Site_Reliability_Engineering">SRE</a> <br>

    Why turtles?
    <br>
    Well, white turtles are cute ( in anime ) and I couldn't think of a better name, that wasn't already taken.
    <br><a href="{{ site.baseurl }}/thissite">See also what went into making this site look like this!</a></p>
</div>

</body>
</html>
