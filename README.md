{%- capture section_settings -%}
{
  "showShippingEstimator": {% if template == 'product.quick-view' or product.available == false %}false{% else %}{{ section.settings.show_shipping_estimator | json }}{% endif %},
  "showQuantitySelector": {{ section.settings.show_quantity_selector | json }},
  "showPaymentButton": {{ section.settings.show_payment_button | json }},
  "showInventoryQuantity": {% if product.template_suffix != 'pre-order' %}{{ section.settings.show_inventory_quantity | json }}{% else %}false{% endif %},
  "lowInventoryThreshold": {{ section.settings.low_inventory_threshold | json }},
  "galleryTransitionEffect": {{ section.settings.carousel_effect | json }},
  "enableImageZoom": {% if template == 'product.quick-view' %}false{% else %}{{ section.settings.enable_image_zoom | json }}{% endif %},
  "zoomEffect": {{ section.settings.zoom_effect | json }},
  "enableVideoLooping": {{ section.settings.enable_video_looping | json }},
  "productOptions": {{ product.options | json | escape }},
  "enableHistoryState": {% if template == 'product.quick-view' %}false{% else %}true{% endif %},
  "infoOverflowScroll": {% if template == 'product.quick-view' %}false{% else %}true{% endif %},
  "isQuickView": {% if template == 'product.quick-view' %}true{% else %}false{% endif %}
}
{%- endcapture -%}


 
<section data-section-id="{{ section.id }}" data-section-type="product" data-section-settings='{{ section_settings }}'>
  {%- if template != 'product.quick-view' -%}
    <div class="container container--flush">
      <div class="page__sub-header">
        <nav aria-label="{{ 'general.breadcrumb.title' | t }}" class="breadcrumb">
          <ol class="breadcrumb__list">
            <li class="breadcrumb__item">
              <a class="breadcrumb__link link" href="{{ routes.root_url }}">{{ 'general.breadcrumb.home' | t }}</a> {%- render 'icon', icon: 'arrow-right' -%}
            </li>

            <li class="breadcrumb__item">
              {%- if collection -%}
                <a class="breadcrumb__link link" href="{{ collection.url }}">{{ collection.title }}</a> {%- render 'icon', icon: 'arrow-right' -%}
              {%- else -%}
                <a class="breadcrumb__link link" href="{{ routes.all_products_collection_url }}">{{- 'collection.general.all_products' | t -}}</a> {%- render 'icon', icon: 'arrow-right' -%}
              {%- endif -%}
            </li>

            <li class="breadcrumb__item">
              <span class="breadcrumb__link" aria-current="page">{{ product.title | truncate: 40 }}</span>
            </li>
          </ol>
        </nav>

         {%- if collection.previous_product or collection.next_product -%}
          <div class="page__navigation">
            {%- if collection.previous_product -%}
              <span class="page__navigation-item page__navigation-item--prev">
                <a href="{{ collection.previous_product.url }}" class="link" rel="prev">{%- render 'icon', icon: 'arrow-left' -%} {{- 'product.general.previous_product' | t -}}</a>
              </span>
            {%- endif -%}

            {%- if collection.next_product -%}
              <span class="page__navigation-item page__navigation-item--next">
                <a href="{{ collection.next_product.url }}" class="link" rel="next">{{- 'product.general.next_product' | t -}} {%- render 'icon', icon: 'arrow-right' -%}</a>
              </span>
            {%- endif -%}
          </div>
        {%- endif -%}
      </div>

      <div class="product-block-list product-block-list--{{ section.settings.image_size }}">
        <div class="product-block-list__wrapper">
          {%- if product.media.size > 0 -%}
            <div class="product-block-list__item product-block-list__item--gallery">
              {% render 'product-gallery' %}
            </div>
          {%- endif -%}

          <div class="product-block-list__item product-block-list__item--info">
            {% render 'product-info' %}
          </div>

          {%- if product.description != blank -%}
            <div class="product-block-list__item product-block-list__item--description">
              <div class="card">
                {%- if section.settings.content_display_mode == 'collapse_all' -%}
                  <button class="card__collapsible-button" data-action="toggle-collapsible" aria-expanded="false" aria-controls="product-description">
                    <span class="card__title heading h3">{{ 'product.general.description' | t }}</span>
                    <span class="plus-button plus-button--large"></span>
                  </button>

                  <div id="product-description" class="card__collapsible">
                    <div class="card__collapsible-content">
                      <div class="rte text--pull">
                        {{ product.description | remove: 'data-section-type="product"' }}
                      </div>
                    </div>
                  </div>
                {%- else -%}
                  <div class="card__header">
                    <h2 class="card__title heading h3">{{ 'product.general.description' | t }}</h2>
                  </div>

                  {%- if section.settings.content_display_mode == 'show_all_and_expand_description' -%}
                    <div class="card__section">
                      <div class="rte text--pull">
                        {{ product.description | remove: 'data-section-type="product"' }}
                      </div>
                    </div>
                  {%- else -%}
                    <div class="card__section expandable-content" aria-expanded="false">
                      <div class="rte text--pull">
                        {{ product.description | remove: 'data-section-type="product"' }}
                      </div>

                      <button class="expandable-content__toggle">
                        <span class="expandable-content__toggle-icon"></span>
                        <span class="expandable-content__toggle-text" data-view-more="{{ 'product.general.view_more' | t | escape }}" data-view-less="{{ 'product.general.view_less' | t | escape }}">{{- 'product.general.view_more' | t -}}</span>
                      </button>
                    </div>
                  {%- endif -%}
                {%- endif -%}
              </div>
            </div>
          {%- endif -%}



          {%- comment -%}
          --------------------------------------------------------------------------------------
          TABS
          --------------------------------------------------------------------------------------
          {%- endcomment -%}

          {%- for i in (1..5) -%}
            {%- assign unique_tab_title = '' -%}
            {%- assign unique_tab_content = '' -%}

            {%- comment -%}First check for metafields{%- endcomment -%}

            {%- assign metafield_tab_title_option = 'tab_' | append: i | append: '_title' -%}
            {%- assign metafield_tab_content_option = 'tab_' | append: i | append: '_content' -%}

            {%- if product.metafields.sf_product_tabs[metafield_tab_title_option] != blank and product.metafields.sf_product_tabs[metafield_tab_content_option] != blank -%}
              {%- assign unique_tab_title = product.metafields.sf_product_tabs[metafield_tab_title_option] -%}
              {%- assign unique_tab_content = product.metafields.sf_product_tabs[metafield_tab_content_option] -%}
            {%- else -%}
              {%- assign tab_to_look = '__tab' | append: i | append: ':' -%}

              {%- for tag in product.tags -%}
                {%- if tag contains tab_to_look -%}
                  {%- assign unique_tab_handle = tag | split: tab_to_look | last -%}
                  {%- assign unique_tab_page = pages[unique_tab_handle] -%}

                  {%- unless unique_tab_page.empty? -%}
                    {%- assign unique_tab_title = unique_tab_page.title -%}
                    {%- assign unique_tab_content = unique_tab_page.content -%}
                    {%- break -%}
                  {%- endunless -%}
                {%- endif -%}
              {%- endfor -%}
            {%- endif -%}

            {%- if unique_tab_title != blank and unique_tab_content != blank -%}
              <div class="product-block-list__item product-block-list__item--content">
                <div class="card">
                  {%- if section.settings.content_display_mode == 'collapse_all_except_description' or section.settings.content_display_mode == 'collapse_all' -%}
                    <button class="card__collapsible-button" data-action="toggle-collapsible" aria-expanded="false" aria-controls="unique-tab-{{ i }}">
                      <span class="card__title heading h3">{{ unique_tab_title }}</span>
                      <span class="plus-button plus-button--large"></span>
                    </button>

                    <div id="unique-tab-{{ i }}" class="card__collapsible">
                      <div class="card__collapsible-content">
                        <div class="rte text--pull">
                          {{ unique_tab_content }}
                        </div>
                      </div>
                    </div>
                  {%- else -%}
                    <div class="card__header">
                      <h2 class="card__title heading h3">{{ unique_tab_title }}</h2>
                    </div>

                    <div class="card__section expandable-content" aria-expanded="false">
                      <div class="rte text--pull">
                        {{ unique_tab_content }}
                      </div>

                      <button class="expandable-content__toggle">
                        <span class="expandable-content__toggle-icon"></span>
                        <span class="expandable-content__toggle-text" data-view-more="{{ 'product.general.view_more' | t | escape }}" data-view-less="{{ 'product.general.view_less' | t | escape }}">{{- 'product.general.view_more' | t -}}</span>
                      </button>
                    </div>
                  {%- endif -%}
                </div>
              </div>
            {%- endif -%}
          {%- endfor -%}

          {%- for i in (1..3) -%}
            {%- assign global_tab_option = 'tab_page_' | append: i | append: '_handle' -%}
            {%- assign global_tab_page = pages[section.settings[global_tab_option]] -%}

            {%- unless global_tab_page.empty? -%}
              <div class="product-block-list__item product-block-list__item--content">
                <div class="card">
                  {%- if section.settings.content_display_mode == 'collapse_all_except_description' or section.settings.content_display_mode == 'collapse_all' -%}
                    <button class="card__collapsible-button" data-action="toggle-collapsible" aria-expanded="false" aria-controls="global-tab-{{ i }}">
                      <span class="card__title heading h3">{{ global_tab_page.title }}</span>
                      <span class="plus-button plus-button--large"></span>
                    </button>

                    <div id="global-tab-{{ i }}" class="card__collapsible">
                      <div class="card__collapsible-content">
                        <div class="rte text--pull">
                          {{ global_tab_page.content }}
                        </div>
                      </div>
                    </div>
                  {%- else -%}
                    <div class="card__header">
                      <h2 class="card__title heading h3">{{ global_tab_page.title }}</h2>
                    </div>

                    <div class="card__section expandable-content" aria-expanded="false">
                      <div class="rte text--pull">
                        {{ global_tab_page.content }}
                      </div>

                      <button class="expandable-content__toggle">
                        <span class="expandable-content__toggle-icon"></span>
                        <span class="expandable-content__toggle-text" data-view-more="{{ 'product.general.view_more' | t | escape }}" data-view-less="{{ 'product.general.view_less' | t | escape }}">{{- 'product.general.view_more' | t -}}</span>
                      </button>
                    </div>
                  {%- endif -%}
                </div>
              </div>
            {%- endunless -%}
          {%- endfor -%}

          {%- comment -%}
          --------------------------------------------------------------------------------------
          REVIEWS
          --------------------------------------------------------------------------------------
          {%- endcomment -%}

          {%- if section.settings.enable_reviews -%}
            <div class="product-block-list__item product-block-list__item--reviews">
              <span id="product-reviews" class="anchor"></span>

              <div class="card">
                <div class="card__section">
                  {%- assign reviews_count = product.metafields.spr.reviews | split: '<meta itemprop="reviewCount" content="' | last | split: '">' | first | times: 1 -%}
                  <div id="shopify-product-reviews" class="spr-reviews {% if reviews_count > 0 %}spr-reviews--has-reviews{% endif %}" data-id="{{ product.id }}">{{ product.metafields.spr.reviews }}</div>
                </div>
              </div>
            </div>
          {%- endif -%}

          {%- comment -%}
          --------------------------------------------------------------------------------------
          SECURITY BADGES
          --------------------------------------------------------------------------------------
          {%- endcomment -%}

          {%- if section.settings.show_payment_icons and shop.enabled_payment_types.size > 0 -%}
            {%- assign show_payment_icons = true -%}
          {%- else -%}
            {%- assign show_payment_icons = false -%}
          {%- endif -%}

          {%- if section.settings.custom_security_badge or show_payment_icons -%}
            <div class="product-block-list__item product-block-list__item--trust">
              <div class="card">
                <div class="card__header card__header--flex">
                  <h2 class="card__title heading h3">{{ section.settings.trust_title | escape }}</h2>

                  {%- render 'icon', icon: 'lock', width: 22, height: 24 -%}
                </div>    
                
                <div class="card__section">
                  {%- if show_payment_icons -%}
                    {%- if section.settings.custom_security_badge -%}
                      <p class="card__subtitle heading h6">{{ 'product.general.payment' | t }}</p>
                    {%- endif -%}

                    <div class="payment-list">
                      
               <svg class="payment-list__item" xmlns="http://www.w3.org/2000/svg" role="img" viewBox="0 0 38 24" width="38" height="24" aria-labelledby="pi-american_express"><title id="pi-american_express">American Express</title><g fill="none"><path fill="#000" d="M35,0 L3,0 C1.3,0 0,1.3 0,3 L0,21 C0,22.7 1.4,24 3,24 L35,24 C36.7,24 38,22.7 38,21 L38,3 C38,1.3 36.6,0 35,0 Z" opacity=".07"></path><path fill="#006FCF" d="M35,1 C36.1,1 37,1.9 37,3 L37,21 C37,22.1 36.1,23 35,23 L3,23 C1.9,23 1,22.1 1,21 L1,3 C1,1.9 1.9,1 3,1 L35,1"></path><path fill="#FFF" d="M8.971,10.268 L9.745,12.144 L8.203,12.144 L8.971,10.268 Z M25.046,10.346 L22.069,10.346 L22.069,11.173 L24.998,11.173 L24.998,12.412 L22.075,12.412 L22.075,13.334 L25.052,13.334 L25.052,14.073 L27.129,11.828 L25.052,9.488 L25.046,10.346 L25.046,10.346 Z M10.983,8.006 L14.978,8.006 L15.865,9.941 L16.687,8 L27.057,8 L28.135,9.19 L29.25,8 L34.013,8 L30.494,11.852 L33.977,15.68 L29.143,15.68 L28.065,14.49 L26.94,15.68 L10.03,15.68 L9.536,14.49 L8.406,14.49 L7.911,15.68 L4,15.68 L7.286,8 L10.716,8 L10.983,8.006 Z M19.646,9.084 L17.407,9.084 L15.907,12.62 L14.282,9.084 L12.06,9.084 L12.06,13.894 L10,9.084 L8.007,9.084 L5.625,14.596 L7.18,14.596 L7.674,13.406 L10.27,13.406 L10.764,14.596 L13.484,14.596 L13.484,10.661 L15.235,14.602 L16.425,14.602 L18.165,10.673 L18.165,14.603 L19.623,14.603 L19.647,9.083 L19.646,9.084 Z M28.986,11.852 L31.517,9.084 L29.695,9.084 L28.094,10.81 L26.546,9.084 L20.652,9.084 L20.652,14.602 L26.462,14.602 L28.076,12.864 L29.624,14.602 L31.499,14.602 L28.987,11.852 L28.986,11.852 Z"></path></g></svg>

              
                <svg class="payment-list__item" viewBox="0 0 38 24" xmlns="http://www.w3.org/2000/svg" role="img" width="38" height="24" aria-labelledby="pi-boleto"><title id="pi-boleto">Boleto</title><path fill="#fff" d="M35.7 23.965H2.3a2.307 2.307 0 0 1-2.3-2.3v-19.4C0 1 1.035-.035 2.3-.035h33.4c1.265 0 2.3 1.035 2.3 2.3v19.4c0 1.265-1.035 2.3-2.3 2.3z"></path><path fill="#A7A8AB" d="M35.564 23.965H2.436c-1.344 0-2.436-1.077-2.436-2.4v-19.2c0-1.323 1.092-2.4 2.436-2.4h33.128c1.344 0 2.436 1.077 2.436 2.4v19.2c0 1.323-1.092 2.4-2.436 2.4zM2.436.925c-.806 0-1.462.646-1.462 1.44v19.2c0 .794.656 1.44 1.462 1.44h33.128c.806 0 1.462-.646 1.462-1.44v-19.2c0-.794-.656-1.44-1.462-1.44H2.436z" opacity=".25"></path><path d="M8.079 4.945h.7v6.298h-.7zm-1.83 0h.7v6.298h-.7zm7.256 0h1.901v6.298h-1.901zm9.715 0h.95v6.298h-.95zm2.324 0h.95v6.298h-.95zm3.804 0h1.221v6.298h-1.221zm-1.375 0h.395v6.298h-.395zm-6.389 0h.395v6.298h-.395zm-.845 0h.395v6.298h-.395zm-2.746 0h.395v6.298h-.395zm-6.31 0h.395v6.298h-.395zm-1.163 0h.733v6.298h-.733zM6.249 19.3v-6.478H8.68c.495 0 .891.065 1.191.196.299.131.532.333.701.606.17.271.255.556.255.855 0 .276-.075.537-.225.781a1.604 1.604 0 0 1-.679.593c.392.115.694.311.903.588.211.276.317.603.317.98 0 .305-.065.587-.193.847a1.644 1.644 0 0 1-.475.603c-.189.14-.425.247-.709.32a4.328 4.328 0 0 1-1.046.109H6.248zm.86-3.755H8.51c.38 0 .653-.026.817-.075a.903.903 0 0 0 .493-.324.936.936 0 0 0 .166-.567 1.03 1.03 0 0 0-.155-.568c-.103-.164-.25-.278-.442-.338s-.52-.09-.985-.09H7.109v1.963zm0 2.995h1.614c.277 0 .472-.011.585-.032.196-.035.362-.094.495-.176a.946.946 0 0 0 .327-.362c.086-.158.128-.341.128-.547 0-.243-.062-.452-.187-.632a.978.978 0 0 0-.516-.377c-.219-.072-.535-.109-.947-.109H7.109v2.235zm4.813-1.588c0-.867.241-1.509.725-1.927.403-.347.896-.52 1.476-.52.644 0 1.172.211 1.582.633.409.421.614 1.004.614 1.748 0 .603-.09 1.077-.271 1.422a1.92 1.92 0 0 1-.792.805 2.292 2.292 0 0 1-1.132.286c-.657 0-1.188-.21-1.594-.63-.406-.421-.608-1.027-.608-1.817zm.814.002c0 .6.131 1.05.394 1.347.264.299.594.448.994.448.395 0 .724-.149.988-.449.262-.3.394-.757.394-1.371 0-.579-.133-1.018-.397-1.315a1.261 1.261 0 0 0-.985-.448c-.4 0-.73.148-.994.445-.262.297-.394.745-.394 1.344zm4.498 2.346v-6.478h.796V19.3h-.796zm5.231-1.52l.823.109c-.128.478-.368.85-.718 1.114-.35.264-.796.397-1.341.397-.685 0-1.227-.211-1.629-.633-.401-.421-.602-1.013-.602-1.775 0-.787.202-1.399.608-1.834.406-.436.932-.653 1.579-.653.626 0 1.137.213 1.534.639.397.427.596 1.027.596 1.8l-.004.211h-3.497c.03.514.175.909.437 1.182a1.3 1.3 0 0 0 .979.41c.291 0 .54-.077.745-.231.207-.154.369-.4.49-.737zm-2.606-1.276h2.615c-.035-.395-.136-.691-.3-.888a1.216 1.216 0 0 0-.983-.46c-.365 0-.671.122-.92.366-.247.244-.385.572-.412.982zm6.164 2.086l.109.703a2.951 2.951 0 0 1-.599.071c-.288 0-.511-.045-.671-.137-.158-.092-.27-.211-.335-.36s-.097-.463-.097-.941v-2.705h-.588v-.615h.588v-1.161l.796-.478v1.639h.796v.615h-.796v2.751c0 .228.014.374.042.439a.324.324 0 0 0 .136.155.53.53 0 0 0 .271.057l.347-.032zm.487-1.638c0-.867.241-1.509.725-1.927.403-.347.896-.52 1.476-.52.644 0 1.172.211 1.582.633.409.421.614 1.004.614 1.748 0 .603-.09 1.077-.271 1.422a1.92 1.92 0 0 1-.792.805 2.292 2.292 0 0 1-1.132.286c-.657 0-1.188-.21-1.594-.63-.406-.421-.608-1.027-.608-1.817zm.814.002c0 .6.131 1.05.394 1.347.264.299.594.448.994.448.395 0 .724-.149.988-.449.262-.3.394-.757.394-1.371 0-.579-.133-1.018-.397-1.315a1.261 1.261 0 0 0-.985-.448c-.4 0-.73.148-.994.445-.262.297-.394.745-.394 1.344z" fill="#221F1F"></path></svg>
              
                <svg class="payment-list__item" role="img" aria-labelledby="pi-elo" width="38" height="24" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 38 24"><title id="pi-elo">Elo</title><g fill-rule="nonzero" fill="none"><path d="M35 0H3C1.3 0 0 1.3 0 3v18c0 1.7 1.4 3 3 3h32c1.7 0 3-1.3 3-3V3c0-1.7-1.4-3-3-3z" fill="#000" opacity=".07"></path><path d="M35 1c1.1 0 2 .9 2 2v18c0 1.1-.9 2-2 2H3c-1.1 0-2-.9-2-2V3c0-1.1.9-2 2-2h32" fill="#FFF"></path><g fill="#000"><path d="M13.3 15.5c-.6.6-1.4.9-2.3.9-.6 0-1.2-.2-1.6-.5l-1.2 1.9c.8.6 1.8.9 2.8.9 1.5 0 2.9-.6 3.9-1.6l-1.6-1.6zm-2.1-7.7c-3 0-5.5 2.4-5.5 5.4 0 1.1.3 2.2.9 3.1l9.8-4.2c-.6-2.5-2.7-4.3-5.2-4.3zm-3.3 5.8v-.4c0-1.8 1.5-3.2 3.2-3.2 1 0 1.8.5 2.4 1.1l-5.6 2.5zm11.6-8.3v10.5l1.8.8-.9 2.1-1.8-.8c-.4-.2-.7-.4-.9-.7-.2-.3-.3-.7-.3-1.3V5.3h2.1zM26 10.2c.3-.1.7-.2 1-.2 1.5 0 2.8 1.1 3.1 2.6l2.2-.4c-.5-2.5-2.7-4.4-5.3-4.4-.6 0-1.2.1-1.7.3l.7 2.1zm-2.6 7.1l1.5-1.7c-.7-.6-1.1-1.4-1.1-2.4s.4-1.8 1.1-2.4l-1.5-1.7c-1.1 1-1.8 2.5-1.8 4.1 0 1.7.7 3.1 1.8 4.1zm6.7-3.4c-.3 1.5-1.6 2.6-3.1 2.6-.4 0-.7-.1-1-.2l-.7 2.1c.5.2 1.1.3 1.7.3 2.6 0 4.8-1.9 5.3-4.4l-2.2-.4z"></path></g></g></svg>
              
                <svg class="payment-list__item" role="img" aria-labelledby="pi-hypercard" viewBox="0 0 38 24" width="38" height="24" xmlns="http://www.w3.org/2000/svg"><title id="pi-hypercard">Hypercard</title><g fill="none" fill-rule="evenodd"><path d="M35 0H3C1.3 0 0 1.3 0 3v18c0 1.7 1.4 3 3 3h32c1.7 0 3-1.3 3-3V3c0-1.7-1.4-3-3-3z" fill="#000" fill-rule="nonzero" opacity=".07"></path><path d="M35 1c1.1 0 2 .9 2 2v18c0 1.1-.9 2-2 2H3c-1.1 0-2-.9-2-2V3c0-1.1.9-2 2-2h32" fill="#FFF" fill-rule="nonzero"></path><path d="M11.9 5.1H8.6c-1.4.1-2.6.6-2.9 1.8-.2.6-.3 1.3-.4 2-.7 3.3-1.3 6.7-2 9.9h25.4c2 0 3.3-.4 3.7-2 .2-.7.3-1.5.5-2.3.6-3.1 1.3-6.2 1.9-9.4H11.9z" fill="#B3131B" fill-rule="nonzero"></path><path d="M6.38 9.31h.605v1.827h2.3V9.31h.605v4.421h-.605v-2.067h-2.3v2.067h-.604v-4.42zm4.364 1.213h.551v3.208h-.55v-3.208zm0-1.213h.551v.614h-.55V9.31zm3.36 3.74c.168-.212.252-.528.252-.95 0-.257-.037-.477-.111-.662-.14-.355-.398-.533-.77-.533-.376 0-.633.188-.771.563a2.23 2.23 0 00-.112.765c0 .248.037.46.112.635.14.333.397.5.77.5a.773.773 0 00.63-.318zm-2.032-2.527h.526v.428c.109-.147.227-.26.355-.34.183-.12.398-.181.644-.181.366 0 .676.14.93.42.255.28.383.68.383 1.2 0 .701-.184 1.203-.551 1.504a1.25 1.25 0 01-.813.286c-.242 0-.446-.053-.61-.16a1.408 1.408 0 01-.323-.31v1.646h-.541v-4.493zm5.477.074c.215.107.378.246.49.417.109.162.181.352.217.569.032.148.048.385.048.71h-2.362c.01.327.087.59.232.787.144.197.368.296.67.296.284 0 .51-.093.678-.28a.944.944 0 00.205-.376h.532c-.014.119-.06.25-.14.396a1.432 1.432 0 01-.266.357c-.165.16-.368.268-.61.325a1.873 1.873 0 01-.443.048c-.402 0-.742-.146-1.02-.438-.28-.292-.419-.7-.419-1.227 0-.517.14-.938.421-1.26.281-.324.648-.485 1.102-.485.229 0 .45.054.665.16zm.199 1.265a1.403 1.403 0 00-.154-.562c-.148-.261-.396-.392-.743-.392a.824.824 0 00-.626.27c-.169.18-.258.408-.268.684h1.79zm1.237-1.354h.514v.557c.042-.108.146-.24.31-.396a.804.804 0 01.62-.23l.124.012v.572a.81.81 0 00-.178-.015c-.273 0-.482.088-.629.263a.92.92 0 00-.22.607v1.853h-.541v-3.223zm4.166.172c.228.176.365.48.411.912h-.526a.972.972 0 00-.22-.495c-.115-.132-.298-.198-.55-.198-.346 0-.593.169-.741.506-.097.219-.145.489-.145.81 0 .323.068.594.205.815.136.22.351.331.644.331.225 0 .403-.068.534-.206.132-.137.222-.325.273-.564h.526c-.06.427-.21.74-.451.937-.241.198-.549.297-.924.297-.422 0-.758-.154-1.008-.462-.251-.308-.377-.693-.377-1.154 0-.566.138-1.007.413-1.322a1.332 1.332 0 011.05-.472c.363 0 .659.088.886.265zm1.54 2.564c.114.09.25.135.406.135.19 0 .375-.044.554-.132a.745.745 0 00.451-.72v-.436a.927.927 0 01-.256.106 2.18 2.18 0 01-.307.06l-.328.042a1.255 1.255 0 00-.442.123c-.167.095-.25.245-.25.452 0 .156.057.28.172.37zm1.14-1.466c.125-.016.208-.068.25-.156a.476.476 0 00.036-.208c0-.185-.065-.318-.197-.402-.131-.083-.32-.125-.564-.125-.283 0-.484.077-.602.23a.752.752 0 00-.13.375h-.505c.01-.397.139-.673.387-.829a1.59 1.59 0 01.862-.233c.38 0 .687.072.924.217.235.144.352.369.352.674v1.857c0 .056.012.101.035.135.023.034.071.051.146.051a.824.824 0 00.177-.018v.4c-.084.025-.148.04-.192.046a1.408 1.408 0 01-.181.009c-.187 0-.322-.067-.406-.199a.767.767 0 01-.094-.298c-.11.145-.269.27-.475.376a1.47 1.47 0 01-.683.16c-.3 0-.544-.091-.733-.273a.905.905 0 01-.285-.681c0-.3.094-.531.28-.695.187-.165.432-.266.735-.304l.863-.109zm1.716-1.27h.514v.557c.043-.108.146-.24.31-.396a.804.804 0 01.62-.23l.124.012v.572a.81.81 0 00-.178-.015c-.273 0-.482.088-.629.263a.92.92 0 00-.22.607v1.853h-.541v-3.223zm2.6 2.516c.147.233.381.35.704.35a.745.745 0 00.619-.324c.161-.216.242-.525.242-.929 0-.407-.083-.708-.25-.904a.78.78 0 00-.617-.294.814.814 0 00-.663.313c-.17.21-.255.516-.255.921 0 .345.074.634.22.867zm1.216-2.417c.096.06.206.166.328.316V9.295h.52v4.436h-.487v-.448a1.172 1.172 0 01-.448.43c-.173.089-.37.133-.593.133-.36 0-.67-.151-.933-.453-.263-.302-.394-.704-.394-1.206 0-.469.12-.876.36-1.22.239-.344.582-.516 1.027-.516.247 0 .453.052.62.156z" fill="#FFF"></path></g></svg>
              
                <svg class="payment-list__item" viewBox="0 0 38 24" xmlns="http://www.w3.org/2000/svg" role="img" width="38" height="24" aria-labelledby="pi-master"><title id="pi-master">Mastercard</title><path opacity=".07" d="M35 0H3C1.3 0 0 1.3 0 3v18c0 1.7 1.4 3 3 3h32c1.7 0 3-1.3 3-3V3c0-1.7-1.4-3-3-3z"></path><path fill="#fff" d="M35 1c1.1 0 2 .9 2 2v18c0 1.1-.9 2-2 2H3c-1.1 0-2-.9-2-2V3c0-1.1.9-2 2-2h32"></path><circle fill="#EB001B" cx="15" cy="12" r="7"></circle><circle fill="#F79E1B" cx="23" cy="12" r="7"></circle><path fill="#FF5F00" d="M22 12c0-2.4-1.2-4.5-3-5.7-1.8 1.3-3 3.4-3 5.7s1.2 4.5 3 5.7c1.8-1.2 3-3.3 3-5.7z"></path></svg>
              
                <svg class="payment-list__item" viewBox="0 0 38 24" xmlns="http://www.w3.org/2000/svg" role="img" width="38" height="24" aria-labelledby="pi-visa"><title id="pi-visa">Visa</title><path opacity=".07" d="M35 0H3C1.3 0 0 1.3 0 3v18c0 1.7 1.4 3 3 3h32c1.7 0 3-1.3 3-3V3c0-1.7-1.4-3-3-3z"></path><path fill="#fff" d="M35 1c1.1 0 2 .9 2 2v18c0 1.1-.9 2-2 2H3c-1.1 0-2-.9-2-2V3c0-1.1.9-2 2-2h32"></path><path d="M28.3 10.1H28c-.4 1-.7 1.5-1 3h1.9c-.3-1.5-.3-2.2-.6-3zm2.9 5.9h-1.7c-.1 0-.1 0-.2-.1l-.2-.9-.1-.2h-2.4c-.1 0-.2 0-.2.2l-.3.9c0 .1-.1.1-.1.1h-2.1l.2-.5L27 8.7c0-.5.3-.7.8-.7h1.5c.1 0 .2 0 .2.2l1.4 6.5c.1.4.2.7.2 1.1.1.1.1.1.1.2zm-13.4-.3l.4-1.8c.1 0 .2.1.2.1.7.3 1.4.5 2.1.4.2 0 .5-.1.7-.2.5-.2.5-.7.1-1.1-.2-.2-.5-.3-.8-.5-.4-.2-.8-.4-1.1-.7-1.2-1-.8-2.4-.1-3.1.6-.4.9-.8 1.7-.8 1.2 0 2.5 0 3.1.2h.1c-.1.6-.2 1.1-.4 1.7-.5-.2-1-.4-1.5-.4-.3 0-.6 0-.9.1-.2 0-.3.1-.4.2-.2.2-.2.5 0 .7l.5.4c.4.2.8.4 1.1.6.5.3 1 .8 1.1 1.4.2.9-.1 1.7-.9 2.3-.5.4-.7.6-1.4.6-1.4 0-2.5.1-3.4-.2-.1.2-.1.2-.2.1zm-3.5.3c.1-.7.1-.7.2-1 .5-2.2 1-4.5 1.4-6.7.1-.2.1-.3.3-.3H18c-.2 1.2-.4 2.1-.7 3.2-.3 1.5-.6 3-1 4.5 0 .2-.1.2-.3.2M5 8.2c0-.1.2-.2.3-.2h3.4c.5 0 .9.3 1 .8l.9 4.4c0 .1 0 .1.1.2 0-.1.1-.1.1-.1l2.1-5.1c-.1-.1 0-.2.1-.2h2.1c0 .1 0 .1-.1.2l-3.1 7.3c-.1.2-.1.3-.2.4-.1.1-.3 0-.5 0H9.7c-.1 0-.2 0-.2-.2L7.9 9.5c-.2-.2-.5-.5-.9-.6-.6-.3-1.7-.5-1.9-.5L5 8.2z" fill="#142688"></path></svg>
              
              <!--<svg class="payment-list__item3" viewBox="0 0 512 512" xmlns="http://www.w3.org/2000/svg" role="img" width="512" height="512" aria-labelledby="pi-pix"><title id="pi-pix">Pix</title>
  <defs/>
  <g fill="#4BB8A9" fill-rule="evenodd">
    <path d="M112.57 391.19c20.056 0 38.928-7.808 53.12-22l76.693-76.692c5.385-5.404 14.765-5.384 20.15 0l76.989 76.989c14.191 14.172 33.045 21.98 53.12 21.98h15.098l-97.138 97.139c-30.326 30.344-79.505 30.344-109.85 0l-97.415-97.416h9.232zm280.068-271.294c-20.056 0-38.929 7.809-53.12 22l-76.97 76.99c-5.551 5.53-14.6 5.568-20.15-.02l-76.711-76.693c-14.192-14.191-33.046-21.999-53.12-21.999h-9.234l97.416-97.416c30.344-30.344 79.523-30.344 109.867 0l97.138 97.138h-15.116z"/>
    <path d="M22.758 200.753l58.024-58.024h31.787c13.84 0 27.384 5.605 37.172 15.394l76.694 76.693c7.178 7.179 16.596 10.768 26.033 10.768 9.417 0 18.854-3.59 26.014-10.75l76.989-76.99c9.787-9.787 23.331-15.393 37.171-15.393h37.654l58.3 58.302c30.343 30.344 30.343 79.523 0 109.867l-58.3 58.303H392.64c-13.84 0-27.384-5.605-37.171-15.394l-76.97-76.99c-13.914-13.894-38.172-13.894-52.066.02l-76.694 76.674c-9.788 9.788-23.332 15.413-37.172 15.413H80.782L22.758 310.62c-30.344-30.345-30.344-79.524 0-109.868"/>
  </g>
</svg>-->
                      
                    </div>

                    <p class="payment-list__notice">{{ 'product.general.payment_notice' | t }}</p>
                  {%- endif -%}

                  {%- if show_payment_icons and section.settings.custom_security_badge -%}
                    <hr class="card__separator">
                  {%- endif -%}

                  {%- if section.settings.custom_security_badge -%}
                    {%- if show_payment_icons -%}
                      <p class="card__subtitle heading h6">{{ 'product.general.security' | t }}</p>
                    {%- endif -%}

                    {%- assign badge_width = section.settings.custom_security_badge.width | at_most: 450 | append: 'x' -%}
                    <img class="product__trust-badge" src="{{ section.settings.custom_security_badge | img_url: badge_width }}" srcset="{{ section.settings.custom_security_badge | img_url: badge_width }} 1x, {{ section.settings.custom_security_badge | img_url: badge_width, scale: 2 }} 2x" alt="{{ section.settings.custom_security_badge.alt | escape }}">
                  {%- endif -%}
                     
                  
                </div>
              </div>
            </div>
          {%- endif -%}

          {%- comment -%}
          --------------------------------------------------------------------------------------
          ESTIMATE SHIPPING
          --------------------------------------------------------------------------------------
          {%- endcomment -%}

          {%- if section.settings.show_shipping_estimator and product.available -%}
            <div class="product-block-list__item product-block-list__item--shipping">
              <div class="card">
                <div class="card__header">
                  <h2 class="card__title heading h3">{{ 'product.general.estimate_shipping' | t }}</h2>
                </div>

                <div class="card__section">
                  <div class="shipping-estimator form">
                    <div class="form__input-row">
                      <div class="form__input-wrapper">
                        <label for="shipping-estimator-country" class="form__label">{{ 'cart.shipping_estimator.country' | t }}</label>

                        <div class="select-wrapper select-wrapper--primary">
                          {%- render 'icon', icon: 'arrow-bottom' -%}

                          <select name="country" id="shipping-estimator-country" data-default="{{ customer.default_address.country | default: section.settings.shipping_estimator_default_country }}" required>
                            {{- country_option_tags -}}
                          </select>
                        </div>
                      </div>

                      <div class="form__input-wrapper" style="display: none">
                        <label for="shipping-estimator-province" class="form__label">{{ 'cart.shipping_estimator.province' | t }}</label>

                        <div class="select-wrapper select-wrapper--primary">
                          {%- render 'icon', icon: 'arrow-bottom' -%}

                          <select name="province" id="shipping-estimator-province" data-default="{{ customer.default_address.province }}"></select>
                        </div>
                      </div>

                      <div class="form__input-wrapper" style="max-width: 155px">
                        <label for="shipping-estimator-zip" class="form__label">{{ 'cart.shipping_estimator.zip_code' | t }}</label>
                        <input type="text" name="zip" id="shipping-estimator-zip" class="form__field form__field--text" value="{{ customer.default.address.zip }}" required>
                      </div>
                    </div>

                    <button type="button" class="form__submit button button--primary" data-action="estimate-shipping">{{ 'cart.shipping_estimator.estimate' | t }}</button>

                    <div class="shipping-estimator__results rte" style="display: none;"></div>
                  </div>
                </div>

                {%- if section.settings.show_refunds_policy and shop.refund_policy != blank -%}
                  <div class="card__section card__section--tight">
                    <button class="product__refund-policy-link link link--accented" data-action="open-modal" aria-controls="modal-refunds-policy">{{ 'product.general.refund_policy' | t }} {%- render 'icon', icon: 'arrow-right' -%}</button>
                  </div>
                {%- endif -%}
              </div>
            </div>
          {%- endif -%}
        </div>
      </div>
      
            <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
<script src="https://unpkg.com/dayjs@1.8.21/dayjs.min.js"></script>
	<script src="https://unpkg.com/dayjs@1.8.21/locale/pt-br.js"></script>
	<script>dayjs.locale('pt-br')</script>
      
      <div id="scrollToTop" class="botaoflutuante">        
      		<span class="button button--primary" id="clicar" data-action="add-to-cart">{{ 'product.form.add_to_cart' | t }}</span>
          
          <script>
            $("#clicar").on("click", function() {
              $(".product-form__add-button").trigger("click");
            });
          </script>
    	</div>
   
  
  </div>
  

   

    {%- if section.settings.show_refunds_policy and shop.refund_policy != blank -%}
      <div id="modal-refunds-policy" class="modal" aria-hidden="true">
        <div class="modal__dialog" role="dialog">
          <header class="modal__header">
            <h3 class="modal__title heading h2">{{ 'product.general.refund_policy' | t }}</h3>
            <button class="modal__close link" data-action="close-modal">{%- render 'icon', icon: 'close' -%}</button>
          </header>

          <div class="modal__content">
            <div class="rte">
              {{ shop.refund_policy }}
            </div>
          </div>
        </div>
      </div>
    {%- endif -%}
  {%- else -%}
    {%- comment -%}We display the quick view within the product template so we can re-use the same settings{%- endcomment -%}
    <div class="featured-product">
      {%- render 'product-gallery' -%}
      {%- render 'product-info' -%}
    </div>
  {%- endif -%}
  
 
  
</section>

{% schema %}
{
  "name": "Página do produto",
  "settings": [
    {
      "type": "header",
      "content": "Frete 1"
    },
    {
      "type": "checkbox",
      "id": "mostrar_frete1",
      "label": "Mostrar",
      "default": true
    },
    {
      "type": "text",
      "id": "titulofrete",
      "label": "Título",
      "default": "Entrega via Correios©"
    },
    {
      "type": "color",
      "id": "cordaborda",
      "label": "Cor",
      "default": "#00bd58"
    },
    {
      "type": "header",
      "content": "Frete 2"
    },
    {
      "type": "checkbox",
      "id": "mostrar_frete2",
      "label": "Mostrar seção",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_fretegratis",
      "label": "Frete Grátis",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_frete_full",
      "label": "Mostrar 'Full'",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_correios",
      "label": "Mostrar Correios",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_devolucao",
      "label": "Devolução grátis",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_garantia",
      "label": "Compra garantida",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_maisvendido",
      "label": "Mais vendido",
      "default": true
    },
    {
      "type": "header",
      "content": "Opções do PIX"
    },
    {
      "type": "checkbox",
      "id": "mostrar_pix",
      "label": "Mostrar barra PIX",
      "default": true
    },
    {
      "type": "text",
      "id": "pix",
      "label": "Porcentagem do desconto",
      "default": "10"
    },
    {
      "type": "header",
      "content": "Contador"
    },
    {
      "type": "paragraph",
      "content": "Para que o contador seja colocado nos produtos em específico, você deve acessar o produto pela interface da Shopify e adicionar uma TAG com o nome de: contador"
    },
    {
      "type": "checkbox",
      "id": "mostrar_contador",
      "label": "Mostrar contador",
      "default": true
    },
    {
      "type": "color",
      "id": "bg_contador",
      "label": "Cor de fundo",
      "default": "#000000"
    },
    {
      "type": "color",
      "id": "txt_um_contador",
      "label": "Cor do texto",
      "default": "#ffffff"
    },
    {
      "type": "text",
      "id": "texto_contador",
      "label": "Texto",
      "default": "Corra! A promoção abaca em "
    },
    {
      "type": "color",
      "id": "txt_dois_contador",
      "label": "Cor do timer + ícone",
      "default": "#ecc90b"
    },
    {
      "type": "header",
      "content": "Bandeiras"
    },
    {
      "type": "color",
      "id": "corbandeiras",
      "label": "Cor do cartão",
      "default": "#00bd58"
    },
    {
      "type": "checkbox",
      "id": "mostrar_bandeiras",
      "label": "Mostrar bandeiras",
      "default": true
    },
    {
      "type": "header",
      "content": "Reviews"
    },
    {
      "type": "checkbox",
      "id": "mostrar_cssryviu",
      "label": "Ryviu estilo Dropmeta",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_cssareviews",
      "label": "Areviews estilo Dropmeta",
      "default": true
    },
    {
      "type": "header",
      "content": "Outras informações"
    },
    {
      "type": "checkbox",
      "id": "mostrar_addtocart",
      "label": "Botão adicionar ao carrinho",
      "default": false
    },
    {
      "type": "checkbox",
      "id": "mostrar_quantidadevendidos",
      "label": "Quantidade de vendidos",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_icone",
      "label": "Ícone de verificado",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_codigodoitem",
      "label": "Código do item + disponível em estoque",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "mostrar_barra_de_entrega",
      "label": "Barra de entrega",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "blackfriday",
      "label": "GIF Pré-Black Friday",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "show_vendor",
      "label": "Mostrar vendedor",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "show_sku",
      "label": "Mostrar SKU",
      "default": false
    },
    {
      "type": "select",
      "id": "selector_mode",
      "label": "Tipo de seletor",
      "options": [
        {
          "value": "block",
          "label": "Bloco"
        },
        {
          "value": "dropdown",
          "label": "Suspenso"
        }
      ],
      "default": "block"
    },
    {
      "type": "select",
      "id": "color_mode",
      "label": "Tipo de seletor da variante",
      "info": "O modo de imagem variante requer que todas as variantes tenham uma imagem associada. [Learn more](https://help.shopify.com/en/manual/products/product-variant-images#add-images-to-existing-variants)",
      "options": [
        {
          "value": "block",
          "label": "Bloco"
        },
        {
          "value": "dropdown",
          "label": "Suspenso"
        },
        {
          "value": "color",
          "label": "Swatch color"
        },
        {
          "value": "variant_image",
          "label": "Imagem da variante"
        }
      ],
      "default": "color"
    },
    {
      "type": "checkbox",
      "id": "show_quantity_selector",
      "label": "Mostrar seletor de quantidade",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "show_payment_button",
      "label": "Show dynamic checkout button",
      "info": "Each customer will see their preferred payment method from those available on your store, such as PayPal or Apple Pay. [Learn more](https://help.shopify.com/manual/using-themes/change-the-layout/dynamic-checkout)",
      "default": true
    },
    {
      "type": "header",
      "content": "Media"
    },
    {
      "type": "paragraph",
      "content": "Learn more about [media types](https://help.shopify.com/en/manual/products/product-media)"
    },
    {
      "type": "select",
      "id": "image_size",
      "label": "Size",
      "info": "Only applicable on desktop screens",
      "options": [
        {
          "value": "small",
          "label": "Small"
        },
        {
          "value": "medium",
          "label": "Medium"
        },
        {
          "value": "large",
          "label": "Large"
        }
      ],
      "default": "small"
    },
    {
      "type": "checkbox",
      "id": "enable_image_zoom",
      "label": "Enable image zoom",
      "default": true
    },
    {
      "type": "checkbox",
      "id": "enable_video_looping",
      "label": "Enable video looping",
      "default": false
    },
    {
      "type": "select",
      "id": "zoom_effect",
      "label": "Zoom effect",
      "options": [
        {
          "value": "inside",
          "label": "Inside"
        },
        {
          "value": "outside",
          "label": "Outside"
        }
      ],
      "default": "outside"
    },
    {
      "type": "select",
      "id": "carousel_effect",
      "label": "Transition effect",
      "options": [
        {
          "value": "fade",
          "label": "Fade"
        },
        {
          "value": "slide",
          "label": "Slide"
        }
      ],
      "default": "fade"
    },
    {
      "type": "header",
      "content": "Inventário"
    },
    {
      "type": "checkbox",
      "id": "show_inventory_quantity",
      "label": "Mostrar quantidade no inventário",
      "default": false
    },
    {
      "type": "range",
      "id": "low_inventory_threshold",
      "label": "Limite de inventário baixo",
      "info": "Use cores de baixo estoque quando a quantidade estiver abaixo do limite. Escolha 0 para sempre mostrar em estoque.",
      "min": 0,
      "max": 100,
      "step": 1,
      "default": 0
    },
    {
      "type": "header",
      "content": "Páginas"
    },
    {
      "type": "select",
      "id": "content_display_mode",
      "label": "Modo de display",
      "options": [
        {
          "value": "show_all",
          "label": "Show all"
        },
        {
          "value": "show_all_and_expand_description",
          "label": "Show all and expand description"
        },
        {
          "value": "collapse_all",
          "label": "Collapse all"
        },
        {
          "value": "collapse_all_except_description",
          "label": "Collapse all except description"
        }
      ],
      "default": "show_all_and_expand_description"
    },
    {
      "type": "page",
      "id": "tab_page_1_handle",
      "label": "Primeira página"
    },
    {
      "type": "page",
      "id": "tab_page_2_handle",
      "label": "Secunda página"
    },
    {
      "type": "page",
      "id": "tab_page_3_handle",
      "label": "Terceira página"
    },
    {
      "type": "header",
      "content": "Avaliações"
    },
    {
      "type": "paragraph",
      "content": "Você precisa instalar um app de avaliação como exemplo do [App Areviews](https://apps.shopify.com/areviews-aliexpress?locale=pt-BR) ou algum outro app do tipo como o Loox, Ryviu ou Ali Reviews."
    },
    {
      "type": "checkbox",
      "id": "enable_reviews",
      "label": "Ativar",
      "default": false
    },
    {
      "type": "checkbox",
      "id": "show_reviews_badge",
      "label": "Mostrar emblema",
      "default": false
    },
    {
      "type": "header",
      "content": "Trust"
    },
    {
      "type": "text",
      "id": "trust_title",
      "label": "Heading",
      "default": "Payment & Security"
    },
    {
      "type": "checkbox",
      "id": "show_payment_icons",
      "label": "Show payment icons",
      "default": true
    },
    {
      "type": "image_picker",
      "id": "custom_security_badge",
      "label": "Security badge",
      "info": "800 x 200px .jpg recommended"
    },
    {
      "type": "header",
      "content": "Shipping estimator"
    },
    {
      "type": "checkbox",
      "id": "show_shipping_estimator",
      "label": "Show shipping estimator",
      "info": "Only show if product is available.",
      "default": true
    },
    {
      "type": "text",
      "id": "shipping_estimator_default_country",
      "label": "Default country",
      "info": "If the customer is logged in, the country of their shipping address will be used.",
      "default": "United States"
    },
    {
      "type": "checkbox",
      "id": "show_refunds_policy",
      "label": "Show refunds policy",
      "info": "Refund policy must be added into your store policies. [Learn more](https://help.shopify.com/manual/sell-online/checkout-settings/refund-privacy-tos).",
      "default": true
    }
  ]
}
{% endschema %}

{% if product.metafields.loox.num_reviews %}
<script id="looxSchemaJson" type="application/ld+json">
{
	"@context": "http://schema.org",
	"@type": "Product",
	"@id": {{ canonical_url | json }},
	"aggregateRating": {
		"@type": "AggregateRating",
		"ratingValue": "{{ product.metafields.loox.avg_rating }}",
		"reviewCount": "{{ product.metafields.loox.num_reviews }}"
	},
	"name": {{ product.title | json }}
}
</script>
{% endif %}


	<div id="looxReviews" data-product-id="{{product.id}}" class="loox-reviews-default">{{ product.metafields.loox.reviews }}</div>
