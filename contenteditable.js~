"use strict"

+( function($) {

    $.fn.contenteditable = function( opt ) {
        var _this = $( this );
        var _opt = {
            editor: null,
        };

        $.extend( _opt, opt );
        $.extend( $.fn.contenteditable.__proto__, _opt.methods );

        _opt.editor.on( 'click', function() {
            _this = $( _this.selector );
            if ( $( 'body' ).is( '.content-editing' ) ) {
                $( 'body' ).removeClass( 'content-editing' );
                _this.each( function() {
                    var _self = $( this );
                    if ( _self.is( '[contenteditable]' ) ) {
                        _self.removeAttr( 'contenteditable' )
                        $.fn.contenteditable[ ( _self.data( 'edit-type' ) || 'text' ) + '_view' ].apply( _self );
                    }
                } );
            } else {
                $( 'body' ).addClass( 'content-editing' );

                _this.each( function() {
                    var _self = $( this );
                    if ( ! _self.is( '[contenteditable]' ) ) {
                        _self.attr( 'contenteditable', 'true' );
                        $.fn.contenteditable[ ( _self.data( 'edit-type' ) || 'text' ) + '_editable' ].apply( _self );
                    }
                } );
            }
        } );
    }

    $.fn.contenteditable.__proto__ = {
        _edit: function() {
            return $( '<i class="cea-icon-edit"></i>' );
        },
        _trash: function() {
            return $( '<i class="cea-icon-trash"></i>' );
        },
        _add: function() {
            return $( '<i class="cea-icon-add"></i>' )
        },
        _file: function( file_selected ) {
            var _self = $( this );

            $( '<input type="file" class="cea-file-input"></input>' )
                .on( 'change', function( event ) {
                    var _s = $( this );
                    var _f = _s.get(0);

                    if ( _f.files && _f.files[0] ) {
                        var reader = new FileReader();
                        reader.onload = function( e ) {
                            _self.attr( 'src', e.target.result );
                        }

                        reader.readAsDataURL( _f.files[0] );

                        if ( file_selected instanceof Function ) {
                            file_selected.apply( _self );
                        }
                    }
                } )
                .insertAfter( _self );
        },

        text_editable: function() {
            var _self = $( this );
            
            if ( $.fn.contenteditable.text_editable_before instanceof Function ) {
                $.fn.contenteditable.text_editable_before.apply( _self );
            }

            if ( $.fn.contenteditable.text_editable_after instanceof Function ) {
                $.fn.contenteditable.text_editable_after.apply( _self );
            }
        },
        text_view: function() {
            var _self = $( this );
            
            if ( $.fn.contenteditable.text_view_before instanceof Function ) {
                $.fn.contenteditable.text_view_before.apply( _self );
            }
            
            if ( $.fn.contenteditable.text_view_after instanceof Function ) {
                $.fn.contenteditable.text_view_after.apply( _self );
            }
        },
        select_editable: function() {
            var _self   = $( this );
            var _val    = _self.html().trim();
            var _select = $( '<select></select>' )
                .addClass( '.cea-selector' );

            $.get( _self.data( 'url' ), function( data ) {
                for ( var idx in data ) {
                    var _k = Object.keys( data[idx] )[0];
                    var _v = data[idx][_k];
                    var _option = $( '<option></option>' )
                        .html( _v )
                        .attr( 'value', _k );

                    if ( _v == _val ) {
                        _option
                            .attr( 'selected', 'selected' )
                            .appendTo( _select );
                    } else {
                        _option
                            .appendTo( _select );
                    }
                }

                _self.html( _select );
            } );
        },
        select_view: function() {
            var _self = $( this );
            var _val = _self.find( 'select' ).val();
            _self.html( _self.find( 'select option[value="' + _val + '"]'  ).html() );
        },
        img_editable: function() {
            var _diff_src = "http://placehold.it/300x200?text=%20";
            var _self = $( this ).clone();
            var _edit = $.fn.contenteditable._edit()
                .addClass( 'left' )
                .on( 'click', function () {
                    _self.nextAll( '.cea-file-input' ).click();
                } );
            var _trash = $.fn.contenteditable._trash()
                .addClass( 'right' )
                .on( 'click', function() {
                    // remove image;
                    _self.attr( 'src', _diff_src );
                    _self
                        .nextAll( '.cea-icon-trash, .cea-icon-edit' )
                        .addClass( 'cea-hidden' );
                    _self
                        .nextAll( '.cea-icon-add' )
                        .removeClass( 'cea-hidden' );
                } );
            var _add = $.fn.contenteditable._add()
                .addClass( 'cea-hidden' )
                .on( 'click', function() {
                    _self.nextAll( '.cea-file-input' ).click();
                } );

            if ( _self.attr( 'src' ) == _diff_src ) {
                _edit.addClass( 'cea-hidden' );
                _trash.addClass( 'cea-hidden' );
            } else {
                _add.addClass( 'cea-hidden' );
            }

            var _box = $( '<div class="cea-img-box"></div>' )
                .append( _self )
                .append( _edit )
                .append( _trash )
                .append( _add )
                .replaceAll( $( this ) );

            $.fn.contenteditable._file.call( _self, function() {
                // show edit and trash icon;
                _self
                    .nextAll( '.cea-icon-trash, .cea-icon-edit' )
                    .removeClass( 'cea-hidden' );
                _self
                    .nextAll( '.cea-icon-add' )
                    .addClass( 'cea-hidden' );
            } );

            return _self;
        },
        img_view: function() {
            var _self   = $( this );
            _self
                .off( 'click' )
                .clone()
                .replaceAll( _self.parents( '.cea-img-box' ) );
        },
        gallery_editable: function() {
            var _self = $( this );

            if ( $.fn.contenteditable.gallery_editable_before instanceof Function ) {
                $.fn.contenteditable.gallery_editable_before.apply( _self );
            }
             
            if ( ! _self.nextAll( '.cea-gallery' ).length ) {
                var _pop = $( `
                    <div class="cea-gallery">
                        <div class="inner">
                            <div class="cea-gallery-editor">
                                <ul>
                                </ul>
                            </div>
                        </div>
                    </div>` )
                    .on( 'click', function() {
                        $( this ).removeClass( 'open' );
                    } );

                _pop.find( '> *' ).on( 'click', function( event ) {
                        if ( event && event.stopPropagation ) {
                            event.stopPropagation();
                        } else {
                            window.event.cancelBubble = true;
                        }
                    } );

                var _add_img = function( img, append ) {
                    var _gallery_editor = _pop.find( '.cea-gallery-editor ul' );

                    var _item = $( `
                        <li class="item cea-img-box">
                            <img />
                        </li>
                        `);

                    _item.find( 'img' ).attr( 'src', img );

                    for ( var idx in append ) {
                        _item.append( append[idx] );
                    }
                    _item.appendTo( _gallery_editor );
                }

                _self.find( '.item img' ).each( function() {
                    var _img = $( this );
                    _add_img( _img.attr( 'src' ), [
                        $.fn.contenteditable._trash().on( 'click', function() {
                            $( this ).parents( '.item.cea-img-box' ).remove();
                        } ),
                    ] );
                } );

                var _add_placeholder = function () {
                    _add_img ( 'http://placehold.it/300?text=%20', [
                        $.fn.contenteditable._trash().addClass( 'cea-hidden' ).on( 'click', function() {
                            $( this ).parents( '.item.cea-img-box' ).remove();
                        } ),
                        $.fn.contenteditable._add().on( 'click', function() {
                            var _img = $( this ).prevAll( 'img' );
                            $.fn.contenteditable._file.call( _img, function() {
                                var _self = $( this );
                                 _self
                                    .nextAll( '.cea-icon-trash' )
                                    .removeClass( 'cea-hidden' );
                                _self
                                    .nextAll( '.cea-icon-add' )
                                    .remove();
                                _add_placeholder();
                            } );
                            _img.nextAll( '.cea-file-input' ).click();
                        } ),
                    ] );
                }
                _add_placeholder();

                _pop.insertAfter( _self );
            }

            if ( ! _self.nextAll( '.cea-masking' ).length ) {
                $( `
                    <div class="cea-masking">
                    </div>
                    ` )
                    .on ( 'click', function() {
                        var _pop = _self.nextAll( '.cea-gallery' );
                        if ( ! _pop.is( '.open' ) ) {
                            _pop.addClass( 'open' );
                        }
                    } )
                    .insertAfter( _self );
            }

            if ( $.fn.contenteditable.gallery_editable_after instanceof Function ) {
                $.fn.contenteditable.gallery_editable_after.apply( _self );
            }
        },
        gallery_view: function () {
            var _self = $( this );

            if ( $.fn.contenteditable.gallery_view_before instanceof Function ) {
                $.fn.contenteditable.gallery_view_before.apply( _self );
            }

            _self
                .nextAll( '.cea-gallery, .cea-masking' )
                .off()
                .remove();

            if ( $.fn.contenteditable.gallery_view_after instanceof Function ) {
                $.fn.contenteditable.gallery_view_after.apply( _self );
            }
        },
    };

    $( document ).ready( function( event ) {
        $( '.contentedit' ).contenteditable( {
            editor: $( '#content-editor' ),
            url: $( '#content-editor' ).data( 'auto-save-url' ),
            methods : {
                gallery_editable_before: function() {
                    var _self   = $( this );
                   
                },
                gallery_view_before: function() {
                },
            },
        } );
    } );

} )( jQuery );

