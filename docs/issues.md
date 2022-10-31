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
function generate_issues(item, style, issues){
    $(item)
        .append(
            `<b>Found ${ issues.length } open issues.</b>`
        );
    $.each(issues, function (i, issue) {
        $(item)
            .append(
                `
                <div class="admonition ${style}">
                <p class="admonition-title">
                    <a href="${ issue.html_url }">#${ issue.number }</a> - ${ issue.title }
                </p>
                <p>
                    ${ converter.makeHtml(issue.body) }
                </p>
                </div>
                `
            )
    });
};

$(document).ready(function () {
    $.getJSON(all_bugs, function (allIssues) {
        generate_issues(".bugs", "warning", allIssues)
    });

    $.getJSON(feature_requests, function (allIssues) {
        generate_issues(".featurerequests", "note", allIssues)
    });
});
    
</script>