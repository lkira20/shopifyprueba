"# shopifyprueba" 

Esto es una prueba tecnica elaborada por luis briceño 

- El schema par los servicios de desarrollo lo desarrolle fijandome del schema de feature schema, que es el que coloca los productos de una colleccion,
en el json del schema con la propiedad presets lo hago visible para agregarlo en la seccion
{{ 'component-card.css' | asset_url | stylesheet_tag }}
{{ 'component-price.css' | asset_url | stylesheet_tag }}

{{ 'component-slider.css' | asset_url | stylesheet_tag }}
{{ 'template-collection.css' | asset_url | stylesheet_tag }}

{% if section.settings.image_shape == 'blob' %}
  {{ 'mask-blobs.css' | asset_url | stylesheet_tag }}
{%- endif -%}

{%- unless section.settings.quick_add == 'none' -%}
  {{ 'quick-add.css' | asset_url | stylesheet_tag }}
  <script src="{{ 'product-form.js' | asset_url }}" defer="defer"></script>
{%- endunless -%}

{%- if section.settings.quick_add == 'standard' -%}
  <script src="{{ 'quick-add.js' | asset_url }}" defer="defer"></script>
{%- endif -%}

{%- if section.settings.quick_add == 'bulk' -%}
  <script src="{{ 'quick-add-bulk.js' | asset_url }}" defer="defer"></script>
  <script src="{{ 'quick-order-list.js' | asset_url }}" defer="defer"></script>
  <script src="{{ 'quantity-popover.js' | asset_url }}" defer="defer"></script>
  <script src="{{ 'price-per-item.js' | asset_url }}" defer="defer"></script>
{%- endif -%}

{%- style -%}
  .section-{{ section.id }}-padding {
    padding-top: {{ section.settings.padding_top | times: 0.75 | round: 0 }}px;
    padding-bottom: {{ section.settings.padding_bottom | times: 0.75 | round: 0 }}px;
  }

  @media screen and (min-width: 750px) {
    .section-{{ section.id }}-padding {
      padding-top: {{ section.settings.padding_top }}px;
      padding-bottom: {{ section.settings.padding_bottom }}px;
    }
  }
{%- endstyle -%}

{%- liquid
  assign products_to_display = section.settings.collection.all_products_count

  if section.settings.collection.all_products_count > section.settings.products_to_show
    assign products_to_display = section.settings.products_to_show
    assign more_in_collection = true
  endif

  assign columns_mobile_int = section.settings.columns_mobile | plus: 0
  assign show_mobile_slider = false
  if section.settings.swipe_on_mobile and products_to_display > columns_mobile_int
    assign show_mobile_slider = true
  endif

  assign show_desktop_slider = false
  if section.settings.enable_desktop_slider and products_to_display > section.settings.columns_desktop
    assign show_desktop_slider = true
  endif
-%}

<div
  class="color-{{ section.settings.color_scheme }} isolate gradient"
>
  <div
    class="collection{% if section.settings.quick_add == 'bulk' %} collection-quick-add-bulk{% endif %} section-{{ section.id }}-padding{% if section.settings.full_width %} collection--full-width{% endif %}"
    id="collection-{{ section.id }}"
    data-id="{{ section.id }}"
  >
    <div class="collection__title title-wrapper title-wrapper--no-top-margin page-width{% if show_mobile_slider %} title-wrapper--self-padded-tablet-down{% endif %}{% if show_desktop_slider %} collection__title--desktop-slider{% endif %}">
      {%- if section.settings.title != blank -%}
        <h2 class="title inline-richtext {{ section.settings.heading_size }}{% if settings.animations_reveal_on_scroll %} scroll-trigger animate--slide-in{% endif %}">
          {{ section.settings.title }}
        </h2>
      {%- endif -%}
      {%- if section.settings.description != blank
        or section.settings.show_description
        and section.settings.collection.description != empty
      -%}
        <div class="collection__description {{ section.settings.description_style }} rte{% if settings.animations_reveal_on_scroll %} scroll-trigger animate--slide-in{% endif %}">
          {%- if section.settings.show_description -%}
            {{ section.settings.collection.description }}
          {%- else -%}
            {{ section.settings.description -}}
          {%- endif %}
        </div>
      {%- endif -%}
    </div>

    <slider-component class="slider-mobile-gutter{% if section.settings.full_width %} slider-component-full-width{% endif %}{% if show_mobile_slider == false %} page-width{% endif %}{% if show_desktop_slider == false and section.settings.full_width == false %} page-width-desktop{% endif %}{% if show_desktop_slider %} slider-component-desktop{% endif %}{% if settings.animations_reveal_on_scroll %} scroll-trigger animate--slide-in{% endif %}">
      <ul
        id="Slider-{{ section.id }}"
        data-id="{{ section.id }}"
        class="grid product-grid contains-card contains-card--product{% if settings.card_style == 'standard' %} contains-card--standard{% endif %} grid--{{ section.settings.columns_desktop }}-col-desktop{% if section.settings.collection == blank %} grid--2-col-tablet-down{% else %} grid--{{ section.settings.columns_mobile }}-col-tablet-down{% endif %}{% if show_mobile_slider or show_desktop_slider %} slider{% if show_desktop_slider %} slider--desktop{% endif %}{% if show_mobile_slider %} slider--tablet grid--peek{% endif %}{% endif %}"
        role="list"
        aria-label="{{ 'general.slider.name' | t }}"
      >
        {%- for product in section.settings.collection.products limit: section.settings.products_to_show -%}
          <li
            id="Slide-{{ section.id }}-{{ forloop.index }}"
            class="grid__item{% if show_mobile_slider or show_desktop_slider %} slider__slide{% endif %}{% if settings.animations_reveal_on_scroll %} scroll-trigger animate--slide-in{% endif %}"
            {% if settings.animations_reveal_on_scroll %}
              data-cascade
              style="--animation-order: {{ forloop.index }};"
            {% endif %}
          >
            {% render 'card-product',
              card_product: product,
              media_aspect_ratio: section.settings.image_ratio,
              image_shape: section.settings.image_shape,
              show_secondary_image: section.settings.show_secondary_image,
              show_vendor: section.settings.show_vendor,
              show_rating: section.settings.show_rating,
              section_id: section.id,
              quick_add: section.settings.quick_add
            %}
          </li>
        {%- else -%}
          {%- for i in (1..section.settings.columns_desktop) -%}
            <li
              class="grid__item{% if settings.animations_reveal_on_scroll %} scroll-trigger animate--slide-in{% endif %}"
              {% if settings.animations_reveal_on_scroll %}
                data-cascade
                style="--animation-order: {{ forloop.index }};"
              {% endif %}
            >
              {% liquid
                assign ridx = forloop.rindex
                case ridx
                  when 5
                    assign ridx = 1
                  when 6
                    assign ridx = 2
                endcase
              %}
              {%- assign placeholder_image = 'product-apparel-' | append: ridx -%}
              {% render 'card-product',
                show_vendor: section.settings.show_vendor,
                media_aspect_ratio: section.settings.image_ratio,
                image_shape: section.settings.image_shape,
                placeholder_image: placeholder_image
              %}
            </li>
          {%- endfor -%}
        {%- endfor -%}
      </ul>
      {%- if show_mobile_slider or show_desktop_slider -%}
        <div class="slider-buttons">
          <button
            type="button"
            class="slider-button slider-button--prev"
            name="previous"
            aria-label="{{ 'general.slider.previous_slide' | t }}"
            aria-controls="Slider-{{ section.id }}"
          >
            {% render 'icon-caret' %}
          </button>
          <div class="slider-counter caption">
            <span class="slider-counter--current">1</span>
            <span aria-hidden="true"> / </span>
            <span class="visually-hidden">{{ 'general.slider.of' | t }}</span>
            <span class="slider-counter--total">{{ products_to_display }}</span>
          </div>
          <button
            type="button"
            class="slider-button slider-button--next"
            name="next"
            aria-label="{{ 'general.slider.next_slide' | t }}"
            aria-controls="Slider-{{ section.id }}"
          >
            {% render 'icon-caret' %}
          </button>
        </div>
      {%- endif -%}
    </slider-component>

    {%- if section.settings.show_view_all and more_in_collection -%}
      <div class="center collection__view-all{% if settings.animations_reveal_on_scroll %} scroll-trigger animate--slide-in{% endif %}">
        <a
          href="{{ section.settings.collection.url }}"
          class="{% if section.settings.view_all_style == 'link' %}link underlined-link{% elsif section.settings.view_all_style == 'solid' %}button{% else %}button button--secondary{% endif %}"
          aria-label="{{ 'sections.featured_collection.view_all_label' | t: collection_name: section.settings.collection.title | escape }}"
        >
          {{ 'sections.featured_collection.view_all' | t }}
        </a>
      </div>
    {%- endif -%}
    {% if section.settings.image_shape == 'arch' %}
      {% render 'mask-arch' %}
    {%- endif -%}
  </div>
</div>

{% schema %}
{
  "name": "Servicios de desarrollo",
  "tag": "section",
  "class": "section",
  "disabled_on": {
    "groups": ["header", "footer"]
  },
  "settings": [
    {
      "type": "inline_richtext",
      "id": "title",
      "default": "Featured collection",
      "label": "t:sections.featured-collection.settings.title.label"
    },
    {
      "type": "select",
      "id": "heading_size",
      "options": [
        {
          "value": "h2",
          "label": "t:sections.all.heading_size.options__1.label"
        },
        {
          "value": "h1",
          "label": "t:sections.all.heading_size.options__2.label"
        },
        {
          "value": "h0",
          "label": "t:sections.all.heading_size.options__3.label"
        }
      ],
      "default": "h1",
      "label": "t:sections.all.heading_size.label"
    },
    {
      "type": "richtext",
      "id": "description",
      "label": "t:sections.featured-collection.settings.description.label"
    },
    {
      "type": "checkbox",
      "id": "show_description",
      "label": "t:sections.featured-collection.settings.show_description.label",
      "default": false
    },
    {
      "type": "select",
      "id": "description_style",
      "label": "t:sections.featured-collection.settings.description_style.label",
      "options": [
        {
          "value": "body",
          "label": "t:sections.featured-collection.settings.description_style.options__1.label"
        },
        {
          "value": "subtitle",
          "label": "t:sections.featured-collection.settings.description_style.options__2.label"
        },
        {
          "value": "uppercase",
          "label": "t:sections.featured-collection.settings.description_style.options__3.label"
        }
      ],
      "default": "body"
    },
    {
      "type": "collection",
      "id": "collection",
      "label": "t:sections.featured-collection.settings.collection.label"
    },
    {
      "type": "range",
      "id": "products_to_show",
      "min": 2,
      "max": 25,
      "step": 1,
      "default": 4,
      "label": "t:sections.featured-collection.settings.products_to_show.label"
    },
    {
      "type": "range",
      "id": "columns_desktop",
      "min": 1,
      "max": 6,
      "step": 1,
      "default": 4,
      "label": "t:sections.featured-collection.settings.columns_desktop.label"
    },
    {
      "type": "checkbox",
      "id": "full_width",
      "label": "t:sections.featured-collection.settings.full_width.label",
      "default": false
    },
    {
      "type": "checkbox",
      "id": "show_view_all",
      "default": true,
      "label": "t:sections.featured-collection.settings.show_view_all.label"
    },
    {
      "type": "select",
      "id": "view_all_style",
      "label": "t:sections.featured-collection.settings.view_all_style.label",
      "options": [
        {
          "value": "link",
          "label": "t:sections.featured-collection.settings.view_all_style.options__1.label"
        },
        {
          "value": "outline",
          "label": "t:sections.featured-collection.settings.view_all_style.options__2.label"
        },
        {
          "value": "solid",
          "label": "t:sections.featured-collection.settings.view_all_style.options__3.label"
        }
      ],
      "default": "solid"
    },
    {
      "type": "checkbox",
      "id": "enable_desktop_slider",
      "label": "t:sections.featured-collection.settings.enable_desktop_slider.label",
      "default": false
    },
    {
      "type": "color_scheme",
      "id": "color_scheme",
      "label": "t:sections.all.colors.label",
      "info": "t:sections.all.colors.has_cards_info",
      "default": "scheme-1"
    },
    {
      "type": "header",
      "content": "t:sections.featured-collection.settings.header.content"
    },
    {
      "type": "select",
      "id": "image_ratio",
      "options": [
        {
          "value": "adapt",
          "label": "t:sections.featured-collection.settings.image_ratio.options__1.label"
        },
        {
          "value": "portrait",
          "label": "t:sections.featured-collection.settings.image_ratio.options__2.label"
        },
        {
          "value": "square",
          "label": "t:sections.featured-collection.settings.image_ratio.options__3.label"
        }
      ],
      "default": "adapt",
      "label": "t:sections.featured-collection.settings.image_ratio.label"
    },
    {
      "type": "select",
      "id": "image_shape",
      "options": [
        {
          "value": "default",
          "label": "t:sections.all.image_shape.options__1.label"
        },
        {
          "value": "arch",
          "label": "t:sections.all.image_shape.options__2.label"
        },
        {
          "value": "blob",
          "label": "t:sections.all.image_shape.options__3.label"
        },
        {
          "value": "chevronleft",
          "label": "t:sections.all.image_shape.options__4.label"
        },
        {
          "value": "chevronright",
          "label": "t:sections.all.image_shape.options__5.label"
        },
        {
          "value": "diamond",
          "label": "t:sections.all.image_shape.options__6.label"
        },
        {
          "value": "parallelogram",
          "label": "t:sections.all.image_shape.options__7.label"
        },
        {
          "value": "round",
          "label": "t:sections.all.image_shape.options__8.label"
        }
      ],
      "default": "default",
      "label": "t:sections.all.image_shape.label",
      "info": "t:sections.all.image_shape.info"
    },
    {
      "type": "checkbox",
      "id": "show_secondary_image",
      "default": false,
      "label": "t:sections.featured-collection.settings.show_secondary_image.label"
    },
    {
      "type": "checkbox",
      "id": "show_vendor",
      "default": false,
      "label": "t:sections.featured-collection.settings.show_vendor.label"
    },
    {
      "type": "checkbox",
      "id": "show_rating",
      "default": false,
      "label": "t:sections.featured-collection.settings.show_rating.label",
      "info": "t:sections.featured-collection.settings.show_rating.info"
    },
    {
      "type": "select",
      "id": "quick_add",
      "default": "none",
      "label": "t:sections.main-collection-product-grid.settings.quick_add.label",
      "info": "t:sections.main-collection-product-grid.settings.quick_add.info",
      "options": [
        {
          "value": "none",
          "label": "t:sections.main-collection-product-grid.settings.quick_add.options.option_1"
        },
        {
          "value": "standard",
          "label": "t:sections.main-collection-product-grid.settings.quick_add.options.option_2"
        },
        {
          "value": "bulk",
          "label": "t:sections.main-collection-product-grid.settings.quick_add.options.option_3"
        }
      ]
    },
    {
      "type": "header",
      "content": "t:sections.featured-collection.settings.header_mobile.content"
    },
    {
      "type": "select",
      "id": "columns_mobile",
      "default": "2",
      "label": "t:sections.featured-collection.settings.columns_mobile.label",
      "options": [
        {
          "value": "1",
          "label": "t:sections.featured-collection.settings.columns_mobile.options__1.label"
        },
        {
          "value": "2",
          "label": "t:sections.featured-collection.settings.columns_mobile.options__2.label"
        }
      ]
    },
    {
      "type": "checkbox",
      "id": "swipe_on_mobile",
      "default": false,
      "label": "t:sections.featured-collection.settings.swipe_on_mobile.label"
    },
    {
      "type": "header",
      "content": "t:sections.all.padding.section_padding_heading"
    },
    {
      "type": "range",
      "id": "padding_top",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "t:sections.all.padding.padding_top",
      "default": 36
    },
    {
      "type": "range",
      "id": "padding_bottom",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "t:sections.all.padding.padding_bottom",
      "default": 36
    }
  ],
  "presets": [
    {
      "name": "Servicios de desarrollo"
    }
  ]
}
{% endschema %}


-el ajax que use para agregar un producto al carrito fue este 
fetch(`${routes.cart_url}`+ '/add.js', {
    method: 'POST',
    headers: {
    'Content-Type': 'application/json'
    },
    body: JSON.stringify(data)
})
.then(response => {
    return response.json();
})
.catch((error) => {
    console.error('Error:', error);
});
-me ayudo bastante a la hora de agregar el producto de regalo cuando se agregara un servicio al carrito

-Para que el carrito solo permita añadir un producto al carrito sencillmaente cada vez q añadiera un producto al carrito previamente elimino todos los productos del carrito con este codigo fetch(`${routes.cart_url}`+ `/clear.js`, {method: 'POST'}) , luego en la promesa devuelta(then) continuo con el proceso de añadir al carrito
-Para que solo se puediera añadir la cantidad de un producto al carrito, modfifique el input de cantidad y le puse el atributo max 1

-