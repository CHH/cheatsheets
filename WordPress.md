# Foundation

* [Bedrock](https://roots.io/bedrock/) enables installing WordPress core and plugins through composer and provides a more sophisticated project structure

# Plugins

## Caching

* [Simple Cache](https://github.com/tlovett1/simple-cache)

## Content Management

* [Advanced Custom Fields Pro](https://www.advancedcustomfields.com/pro/)

## SEO

* [All in One SEO Pack](https://fuc.wordpress.org/plugins/all-in-one-seo-pack/)

## Utilities

* [Soil](https://roots.io/plugins/soil/): Cleans up WordPress’ header tags

# Theming

## Writing your own theme

All you need is an `index.php` with the loop in it and a `style.css` for the theme metadata. This is used for all pages. Componentize as needed, i.e. with `get_template_part()` and your own template tags.

[More about basic templates in the Theme Development manual](https://codex.wordpress.org/Theme_Development#Basic_Templates)

## Starter themes

* [Underscores](http://underscores.me) is a minimal WordPress starter theme from Automattic

# This & that

## Generate an excerpt for posts using ACF's flexible content

```php
if (!function_exists('my_custom_excerpt_content')):
  // Fetches the excerpt content from the current post's flexible content rows
	function my_custom_excerpt_content() {
		global $post;
		
		// The post's custom field which contains the flexible content rows
		$flexibleContentField = 'flexible_content';
		// Name of the row's layout type which we want to select
		$layoutTypeForExcerpt = 'text';
		// Name of the layout's field which contains the actual content
		$contentFieldOfLayout = 'text';
		
		while (have_rows($flexibleContentField)): the_row();
		  // Select the first layout which contains text content, which we want to use for the excerpt
			if (get_row_layout() === $layoutTypeForExcerpt) {
				return get_sub_field($contentFieldOfLayout);
			}
		endwhile;
	}
endif;

if (function_exists('have_rows') && !function_exists('my_custom_excerpt')):
	function my_custom_excerpt() {
	  global $post;
	  
	  if (null === $text = my_custom_excerpt_content()) {
      // Fallback to manual excerpt and post content for posts not using flexible content
	    $text = $post->post_excerpt ?: $post->post_content;
    }

		if (!empty($text)) {
			$text = strip_shortcodes( $text );
			$text = apply_filters('the_content', $text);
			$text = str_replace(']]>', ']]>', $text);
			$excerpt_length = apply_filters('excerpt_length', 20);
			$excerpt_more = apply_filters('excerpt_more', '…');
			$text = wp_trim_words( $text, $excerpt_length, $excerpt_more );
		}
		return apply_filters('the_excerpt', $text);
	}
	
	// Add the function as filter when `the_excerpt` or `get_the_excerpt` are called in templates
	add_filter('get_the_excerpt', 'my_custom_excerpt');
endif;
```
