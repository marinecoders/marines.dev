# Marine Coder Issues

## Feature Requests
<div class="featurerequests"></div>

## Bugs
<div class="bugs"></div>

<script src="https://code.jquery.com/jquery-3.6.1.min.js" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/showdown/dist/showdown.min.js"></script>

<script>
var converter = new showdown.Converter()

//var other_issues = "https://api.github.com/repos/marinecoders/marines.dev/issues?state=open";
var all_bugs = "https://api.github.com/repos/marinecoders/marines.dev/issues?state=open&labels=bug";
var feature_requests = "https://api.github.com/repos/marinecoders/marines.dev/issues?state=open&labels=feature%20request"

/* 
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
massa, nec semper lorem quam in massa.</p>
</div>

html_url: "https://github.com/marinecoders/marines.dev/issues/13"
*/

$(document).ready(function () {
    $.getJSON(all_bugs, function (allIssues) {
        $(".bugs")
            .append(
                "<b>Found " + allIssues.length + " open Bugs.</b>"
            );
        $.each(allIssues, function (i, issue) {
            console.log(issue)
            $(".bugs")
                .append(
                    "<div class=\"admonition warning\">" + 
                    "<p class=\"admonition-title\">" + "<a href=\"" + issue.html_url + "\"> #" + issue.number + "</a> - " + issue.title + "</p>" +
                    "<p>" + converter.makeHtml(issue.body) + 
                    "</p></div>"
                )
        });
    });
    $.getJSON(feature_requests, function (allIssues) {
        $(".featurerequests")
            .append(
                "<b>Found " + allIssues.length + " open Feature Request.</b>"
            );
        $.each(allIssues, function (i, issue) {
            console.log(issue)
            $(".featurerequests")
                .append(
                    "<div class=\"admonition abstract\">" + 
                    "<p class=\"admonition-title\">" + "<a href=\"" + issue.html_url + "\"> #" + issue.number + "</a> - " + issue.title + "</p>" +
                    "<p>" + converter.makeHtml(issue.body) + 
                    "</p></div>"
                )
        });
    });
});
    
</script>