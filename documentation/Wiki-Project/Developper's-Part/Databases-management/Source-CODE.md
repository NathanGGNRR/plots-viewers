# **WORK ITEM MENTION:**
#29
#31

# The data source script to create the Database

## **SCHEMA**:
![image.png](/.attachments/image-7cde1221-2ac7-4163-9361-d714248de632.png)

## **CODE:**     
```
CREATE OR REPLACE FUNCTION public.update_time()
 RETURNS trigger
 LANGUAGE plpgsql
AS $function$
	begin
		new.updated_at := current_date;
	    return new;
	end; 
$function$
;

CREATE TABLE public."geometry_tb" (
    id serial NOT NULL,
    geom geometry(MULTIPOLYGON, 4326) NULL,
    updated_at timestamp NULL,
    CONSTRAINT geometry_pkey PRIMARY KEY (id)
);
CREATE INDEX sidx_geometry_geom ON public."geometry_tb" USING gist (geom);
ALTER TABLE public."geometry_tb" OWNER TO "geomAdmin";
GRANT ALL ON TABLE public."geometry_tb" TO "geomAdmin";

create trigger trigger_update_time_geometry_tb before
update
    on
    public.geometry_tb for each row execute procedure update_time();

CREATE TABLE country(
    gid text NOT NULL,
    nom text NULL,
    id_geometry integer REFERENCES public."geometry_tb"(id),
    updated_at timestamp NULL,
    CONSTRAINT country_pkey PRIMARY KEY (gid)
);
ALTER TABLE public.country OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.country TO "geomAdmin";

create trigger trigger_update_time_country before
update
    on
    country for each row execute procedure update_time();

CREATE TABLE regions(
    gid text NOT NULL,
    nom text NULL,
    id_geometry integer REFERENCES public."geometry_tb"(id),
    gid_country text REFERENCES country(gid),
    updated_at timestamp NULL,
    CONSTRAINT regions_pkey PRIMARY KEY (gid)
);
ALTER TABLE public.regions OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.regions TO "geomAdmin";

create trigger trigger_update_time_regions before
update
    on
    regions for each row execute procedure update_time();

CREATE TABLE departements(
    id text NOT NULL,
    nom text NULL,
    id_geometry integer REFERENCES public."geometry_tb"(id),
    gid_region text REFERENCES regions(gid),
    updated_at timestamp NULL,
    CONSTRAINT departements_pkey PRIMARY KEY (id)
);
ALTER TABLE public.departements OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.departements TO "geomAdmin";

create trigger trigger_update_time_departements before
update
    on
    departements for each row execute procedure update_time();

CREATE TABLE communes(
    id text NOT NULL,
    nom text NULL,
    id_geometry integer REFERENCES public."geometry_tb"(id),
    id_departement text REFERENCES departements(id),
    updated_at timestamp NULL,
    CONSTRAINT communes_pkey PRIMARY KEY (id)
);
ALTER TABLE public.communes OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.communes TO "geomAdmin";

create trigger trigger_update_time_communes before
update
    on
    communes for each row execute procedure update_time();

CREATE TABLE vignobles(
    id serial NOT NULL,
    nom text NULL,
    updated_at timestamp NULL,
    CONSTRAINT vignobles_pkey PRIMARY KEY (id)
);
ALTER TABLE public.vignobles OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.vignobles TO "geomAdmin";

create trigger trigger_update_time_vignobles before
update
    on
    vignobles for each row execute procedure update_time();

CREATE TABLE hierarchies(
    id serial NOT NULL,
    nom text NULL,
    updated_at timestamp NULL,
    CONSTRAINT hierarchies_pkey PRIMARY KEY (id)
);
ALTER TABLE public.hierarchies OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.hierarchies TO "geomAdmin";

create trigger trigger_update_time_hierarchies before
update
    on
    hierarchies for each row execute procedure update_time();

CREATE TABLE appellations(
    id serial NOT NULL,
    nom text NULL,
    id_vignoble integer REFERENCES vignobles(id),
    id_hierarchie integer REFERENCES hierarchies(id),
    updated_at timestamp NULL,
    CONSTRAINT appellations_pkey PRIMARY KEY (id)
);
ALTER TABLE public.appellations OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.appellations TO "geomAdmin";

create trigger trigger_update_time_appellations before
update
    on
    appellations for each row execute procedure update_time();

CREATE TABLE plots(
    id serial NOT NULL,
    type_ig text NULL,
    id_apellation integer REFERENCES appellations(id),
    id_geometry integer REFERENCES public."geometry_tb"(id),
    insee text REFERENCES communes(id),
    updated_at timestamp NULL,
    CONSTRAINT plots_pkey PRIMARY KEY (id)
);
ALTER TABLE public.plots OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.plots TO "geomAdmin";


create trigger trigger_update_time_plots before
update
    on
    plots for each row execute procedure update_time();

CREATE TABLE lieux_dits(
    id serial NOT NULL,
    nom text NULL,
    id_geometry integer REFERENCES public."geometry_tb"(id),
    insee text REFERENCES communes(id),
    updated_at timestamp NULL,
    CONSTRAINT lieux_dits_pkey PRIMARY KEY (id)
);
ALTER TABLE public.lieux_dits OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.lieux_dits TO "geomAdmin";

create trigger trigger_update_time_lieux_dits before
update
    on
    lieux_dits for each row execute procedure update_time();

CREATE TABLE parcelles(
    id text NOT NULL,
    prefixe text NULL,
    "section" text NULL,
    numero text NULL,
    contenance float8 NULL,
    arpente bool NULL,
    id_geometry integer REFERENCES public."geometry_tb"(id),
    id_lieux_dit integer REFERENCES public.lieux_dits(id) NULL,
    insee text REFERENCES communes(id),
    updated_at timestamp NULL,
    CONSTRAINT parcelles_pkey PRIMARY KEY (id)
);
ALTER TABLE public.parcelles OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.parcelles TO "geomAdmin";

create trigger trigger_update_time_parcelles before
update
    on
    parcelles for each row execute procedure update_time();

CREATE TABLE elevations(
    id serial NOT NULL,
    dn integer NULL,
    id_geometry integer REFERENCES public."geometry_tb"(id),
    updated_at timestamp NULL,
    CONSTRAINT elevations_pkey PRIMARY KEY (id)
);
ALTER TABLE public.elevations OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.elevations TO "geomAdmin";

create trigger trigger_update_time_elevations before
update
    on
    elevations for each row execute procedure update_time();

create table langues (
	id serial not null,
	code text null,
	CONSTRAINT langues_pkey PRIMARY KEY (id)
);
ALTER TABLE public.langues OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.langues TO "geomAdmin";

create trigger trigger_update_time_langues before
update
    on
    public.langues for each row execute procedure update_time();

create table translations (
	id serial not null,
	id_langue integer references langues(id),
	"translate" text null,
	CONSTRAINT translations_pkey PRIMARY KEY (id)
);
ALTER TABLE public.translations OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.translations TO "geomAdmin";

create trigger trigger_update_time_translations before
update
    on
    public.translations for each row execute procedure update_time();


create table link_translations (
	id serial not null,
	id_translation integer references translations(id),
	gid_country text references country(gid) null,
	gid_region text references regions(gid) null,
	id_departement text references departements(id) null,
	CONSTRAINT link_translations_pkey PRIMARY KEY (id)
);
ALTER TABLE public.link_translations OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.link_translations TO "geomAdmin";

create trigger trigger_update_time_link_translations before
update
    on
    public.link_translations for each row execute procedure update_time();
    

create table link_elevations_commune (
	id serial not null,
	id_elevation integer references elevations(id) NULL,
	insee text references communes(id) null,
	CONSTRAINT link_elevations_commune_pkey PRIMARY KEY (id)
);
ALTER TABLE public.link_elevations_commune OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.link_elevations_commune TO "geomAdmin";
  
create trigger trigger_update_time_link_elevations_commune before
update
    on
    public.link_elevations_commune for each row execute procedure update_time();


CREATE TABLE public.appellations (
	id serial NOT NULL,
	"name" text NULL,
	updated_at timestamp NULL,
	CONSTRAINT appellationsnew_pkey PRIMARY KEY (id)
);

ALTER TABLE public.appellations OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.appellations TO "geomAdmin";
       
create trigger trigger_update_time_appellations before
update
    on
    public.appellations for each row execute procedure update_time();



CREATE TABLE public.superficie (
	id serial NOT NULL,
	insee text NOT NULL,
	superficie float8 NULL,
	updated_at timestamp NULL,
	CONSTRAINT superficie_pkey PRIMARY KEY (id),
	CONSTRAINT superficie_insee_fkey FOREIGN KEY (insee) REFERENCES communes(id)
);

ALTER TABLE public.superficie OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.superficie TO "geomAdmin";

create trigger trigger_update_time_superficie before
update
    on
    public.superficie for each row execute procedure update_time();


CREATE TABLE public.stats_product (
	id serial NOT NULL,
	id_departement text NOT NULL,
	departname text NOT NULL,
	nbdeclarations text NULL,
	superficie_totale text NULL,
	aop_superficie text NULL,
	aoc_superficie text NULL,
	vdqs_superficie text NULL,
	apte_cognac_armagnac_superficie text NULL,
	igp_superficie text NULL,
	vsig_superficie text NULL,
	autre_superficie text NULL,
	blanc_aoc_quantite text NULL,
	rouge_rose_aoc_quantite text NULL,
	blanc_vdqs_quantite text NULL,
	rouge_rose_quantite text NULL,
	blanc_aop_quantite text NULL,
	rouge_aop_quantite text NULL,
	rose_aop_quantite text NULL,
	vsi_vci_aop_quantite text NULL,
	blanc_igp_quantite text NULL,
	rouge_igp_quantite text NULL,
	rose_igp_quantite text NULL,
	vci_igp_quantite text NULL,
	blanc_vsig_quantite text NULL,
	rouge_vsig_quantite text NULL,
	rose_vsig_quantite text NULL,
	apte_cognac_armagnac text NULL,
	blanc_pays text NULL,
	rouge_rose_pays text NULL,
	blanc_autre text NULL,
	rouge_rose_autre text NULL,
	blanc_quantite_totale text NULL,
	rouge_rose_quantite_totale text NULL,
	commercialisable text NULL,
	non_commercialisable text NULL,
	annee text NULL,
	total text NULL,
	updated_at timestamp NULL,
	CONSTRAINT stats_product_pkey PRIMARY KEY (id),
	CONSTRAINT stats_product_id_departement_fkey FOREIGN KEY (id_departement) REFERENCES departements(id)
);

ALTER TABLE public.stats_product OWNER TO "geomAdmin";
GRANT ALL ON TABLE public.stats_product TO "geomAdmin";    
    
create trigger trigger_update_time_stats_product before
update
    on
    public.stats_product for each row execute procedure update_time();
    

    
    

    


CREATE OR REPLACE PROCEDURE public.retrieve_data()
 LANGUAGE plpgsql
AS $procedure$
    
    declare
    
        inserted_cadastre_id integer;
        inserted_vignoble_id integer;
        inserted_hierarchie_id integer;
        inserted_departement_id integer;
        inserted_region_id integer;
        inserted_country_id integer;
        inserted_communes_id integer;
    inserted_appellation_id integer;
    
        denomination_rows RECORD;
        plots_rows RECORD;
        country_rows RECORD;
        region_rows RECORD;
        departement_rows RECORD;
        communes_rows RECORD;
        elevation_rows RECORD;
        parcelles_rows RECORD;
        lieux_dits_rows RECORD;
       
        cursor_denomination cursor for select id, nomapp, hierarchie, vignoble from denominationsold;
        cursor_plots cursor for select id, geom, new_insee, new_nomcom, type_ig, appellation, denomination from plotsold;
        cursor_country cursor for select "GID_0", geom, "NAME_0" from public."delimCountry";
        cursor_region cursor for select "GID_1", "GID_0", geom, "NAME_1" from public."delimRegion";
        cursor_departement cursor for select geom, "CC_2", "NAME_2", "GID_1" from public."delimDepartment";
        cursor_communes cursor for select id, geom, nom, updated_at from public."cadastre-communes";
        cursor_elevation cursor for select id, geom, dn from elevation;
        cursor_parcelles cursor for select id, geom, prefixe, "section", numero, contenance, arpente, commune, updated_at from public."cadastre-parcelles";
        cursor_lieux_dits cursor for select geom, nom, commune, updated_at from public."cadastre-lieux_dits";
    
    BEGIN
        
        open cursor_denomination;
            loop
                fetch cursor_denomination into denomination_rows;
                EXIT when not found;
            
                /* VIGNOBLE */
                if (select count(*) from vignobles where vignobles.nom = denomination_rows.vignoble) = 0 then
                    insert into vignobles (nom, updated_at) values (denomination_rows.vignoble, current_timestamp) returning id into inserted_vignoble_id;
                else
                    select id into inserted_vignoble_id from vignobles where nom = denomination_rows.vignoble;
                end if;
                /* END VIGNOBLE */
            
                /* HIERARCHIE */
                if (select count(*) from hierarchies where hierarchies.nom = denomination_rows.hierarchie) = 0 then
                    insert into hierarchies (nom, updated_at) values (denomination_rows.hierarchie, current_timestamp) returning id into inserted_hierarchie_id;
                else
                    select id into inserted_hierarchie_id from hierarchies where nom = denomination_rows.hierarchie;
                end if;
                /* END HIERARCHIE */
            
                /* APPELLATION */
                INSERT INTO denominations (id, nom, id_vignoble, id_hierarchie, updated_at) VALUES (denomination_rows.id, denomination_rows.nomapp, inserted_vignoble_id, inserted_hierarchie_id, CURRENT_TIMESTAMP);
                /* END APPELLATION */
            
            end loop;
        close cursor_denomination;
    
    
         /** COUNTRY **/
        open cursor_country;
            loop
                fetch cursor_country into country_rows;
                exit when not found;
            
                INSERT INTO geometry_tb (geom, updated_at) VALUES (ST_SetSRID(ST_Multi(country_rows.geom), 4326), CURRENT_TIMESTAMP) returning id INTO inserted_cadastre_id;
                insert into country (gid, nom, id_geometry, updated_at) values (country_rows."GID_0", country_rows."NAME_0", inserted_cadastre_id, CURRENT_TIMESTAMP);
            
            end loop;
        close cursor_country;
        /** END COUNTRY **/
    
    
        /** REGION **/
        open cursor_region;
            loop
                fetch cursor_region into region_rows;
                exit when not found;
            
                INSERT INTO geometry_tb (geom, updated_at) VALUES (ST_SetSRID(ST_Multi(region_rows.geom), 4326), CURRENT_TIMESTAMP) returning id INTO inserted_cadastre_id;
                insert into regions (gid, nom, id_geometry, gid_country, updated_at) values (region_rows."GID_1", region_rows."NAME_1", inserted_cadastre_id, region_rows."GID_0", current_timestamp);
            
            end loop;
        close cursor_region;
        /** END REGION **/
    
    
        /** DEPARTEMENT **/
        open cursor_departement;
            loop
                fetch cursor_departement into departement_rows;
                exit when not found;
            
                INSERT INTO geometry_tb (geom, updated_at) VALUES (ST_SetSRID(ST_Multi(departement_rows.geom), 4326), CURRENT_TIMESTAMP) returning id INTO inserted_cadastre_id;
                if departement_rows."GID_1" = '' then
                    insert into departements (id, nom, id_geometry, gid_region, updated_at) values (departement_rows."CC_2", departement_rows."NAME_2", inserted_cadastre_id, null, current_timestamp);
                else
                    insert into departements (id, nom, id_geometry, gid_region, updated_at) values (departement_rows."CC_2", departement_rows."NAME_2", inserted_cadastre_id, departement_rows."GID_1", current_timestamp);
                end if;
            
            end loop;
        close cursor_departement;
        /** END DEPARTEMENT **/
    
        
        /** COMMUNES **/
        open cursor_communes;
            loop
                fetch cursor_communes into communes_rows;
                exit when not found;
            
                INSERT INTO geometry_tb (geom, updated_at) VALUES (ST_SetSRID(ST_Multi(communes_rows.geom), 4326), CURRENT_TIMESTAMP) returning id INTO inserted_cadastre_id;
                if SUBSTRING(communes_rows.id, 1, 2) = '97' then
                    insert into communes (id, nom, id_geometry, id_departement, updated_at) values (communes_rows.id, communes_rows.nom, inserted_cadastre_id, SUBSTRING(communes_rows.id, 1, 3), current_timestamp);
                else
                    insert into communes (id, nom, id_geometry, id_departement, updated_at) values (communes_rows.id, communes_rows.nom, inserted_cadastre_id, SUBSTRING(communes_rows.id, 1, 2), current_timestamp);
                end if;
            
            end loop;
        close cursor_communes;
        /** END COMMUNES **/
    
    /** PLOTS **/
        open cursor_plots;
            loop
                FETCH cursor_plots INTO plots_rows;
                exit WHEN NOT FOUND;
            
                if (select count(*) from communes where id = plots_rows.new_insee) != 0  then
                    INSERT INTO geometry_tb (geom, updated_at) VALUES (ST_SetSRID(ST_Multi(plots_rows.geom), 4326), CURRENT_TIMESTAMP) returning id INTO inserted_cadastre_id;
                
                    if (select count(*) from appellations where appellations."name" = plots_rows.appellation) = 0  then
                        insert into appellations ("name") values (plots_rows.appellation) returning id into inserted_appellation_id;
                    end if;
                
                    update denominations set id_appellation = inserted_appellation_id where nom = plots_rows.denomination;
                    insert into plots (id, type_ig, id_denomination, id_geometry, insee, updated_at) values (plots_rows.id, plots_rows.type_ig, (select id from denominations where nom = plots_rows.denomination), inserted_cadastre_id, plots_rows.new_insee, current_timestamp);
                
                end if;
            
            END loop;
        CLOSE cursor_plots;
        /** END PLOTS **/
    
       
        /** ELEVATION **/
        open cursor_elevation;
            loop
                fetch cursor_elevation into elevation_rows;
                exit when not found;
            
                INSERT INTO geometry_tb (geom, updated_at) VALUES (ST_SetSRID(ST_Multi(elevation_rows.geom), 4326), CURRENT_TIMESTAMP) returning id INTO inserted_cadastre_id;
                insert into elevations (id, dn, id_geometry, updated_at) values (elevation_rows.id, elevation_rows.dn, inserted_cadastre_id, CURRENT_TIMESTAMP);
            
            end loop;
        close cursor_elevation;
        /** END ELEVATION **/
       
       
       /** PARCELLES **/
        open cursor_parcelles;
            loop
                fetch cursor_parcelles into parcelles_rows;
                exit when not found;
            
                if (select count(*) from communes where id = parcelles_rows.commune) != 0  then
                INSERT INTO geometry_tb (geom, updated_at) VALUES (ST_SetSRID(ST_Multi(parcelles_rows.geom), 4326), CURRENT_TIMESTAMP) returning id INTO inserted_cadastre_id;
                insert into parcelles (id, prefixe, "section", numero, contenance, arpente, id_geometry, insee, updated_at) values (parcelles_rows.id, parcelles_rows.prefixe, parcelles_rows."section", parcelles_rows.numero, parcelles_rows.contenance, parcelles_rows.arpente, inserted_cadastre_id, parcelles_rows.commune, parcelles_rows.updated_at);
               end if;
            
            end loop;
        close cursor_parcelles;
        /** END PARCELLES **/
       
 /** LIEUX_DITS **/
        open cursor_lieux_dits;
            loop
                fetch cursor_lieux_dits into lieux_dits_rows;
                exit when not found;
            
                if (select count(*) from communes where id = lieux_dits_rows.commune) != 0  then
                INSERT INTO geometry_tb (geom, updated_at) VALUES (ST_SetSRID(ST_Multi(lieux_dits_rows.geom), 4326), CURRENT_TIMESTAMP) returning id INTO inserted_cadastre_id;
                insert into lieux_dits (nom, id_geometry, insee, updated_at) values (lieux_dits_rows.nom, inserted_cadastre_id, lieux_dits_rows.commune, lieux_dits_rows.updated_at);
               end if;
            
            end loop;
        close cursor_lieux_dits;
        /** END LIEUX_DITS **/
        
        CALL add_link_elevations_commune();
        CALL add_lieux_dits_into_parcelles();
    end;
$procedure$
;

CREATE OR REPLACE PROCEDURE public.add_link_elevations_commune()
 LANGUAGE plpgsql
AS $procedure$
	declare 
		 elevation_row RECORD;
		 commune_row RECORD;
	     curs_elevations cursor for select e.id, geom from elevations E inner join geometry_tb G on E.id_geometry = G.id;
	     curs_communes cursor for select c.id, geom from communes C inner join geometry_tb G on C.id_geometry = G.id;
	BEGIN

		open curs_elevations;
		 LOOP
	        FETCH curs_elevations INTO elevation_row;
	        EXIT WHEN NOT FOUND;
	        open curs_communes;
				LOOP
			       FETCH curs_communes INTO commune_row;
			       EXIT WHEN NOT FOUND;
			       if ST_INTERSECTS(elevation_row.geom,commune_row.geom) = true then
			        insert into link_elevations_commune(id_elevation, insee) values (elevation_row.id, commune_row.id);
		           end if;
			    END LOOP;
			 CLOSE curs_communes;
	     END LOOP;
		 CLOSE curs_elevations;
	END;
$procedure$
;


CREATE OR REPLACE PROCEDURE public.add_lieux_dits_into_parcelles()
	LANGUAGE plpgsql
AS $$
	declare 
		 parcelle_row RECORD;
		 lieux_dit_row RECORD;
	     curs_parcelles cursor for select id, geom from parcelles P inner join geometry_tb G on P.id_geometry =G.id;
	     curs_lieux_dits cursor for select id, geom from lieux_dits L inner join geometry_tb G on L.id_geometry = G.id;
	BEGIN

		open curs_parcelles;
		 LOOP
	        FETCH curs_parcelles INTO parcelle_row;
	        EXIT WHEN NOT FOUND;
	        open curs_lieux_dits;
				LOOP
			       FETCH curs_lieux_dits INTO lieux_dit_row;
			       EXIT WHEN NOT FOUND;
			       if ST_INTERSECTS(parcelle_row.geom,lieux_dit_row.geom) = true then
			        UPDATE parcelles set id_lieux_dit = lieux_dit_row.id WHERE id = parcelle_row.id;
                    FETCH curs_parcelles INTO parcelle_row;
		           end if;
			    END LOOP;
			 CLOSE curs_lieux_dits;
	     END LOOP;
		 CLOSE curs_lieux_dits;
	END;
$$
;

CREATE OR REPLACE PROCEDURE public.add_commune(url text, insee text)
 LANGUAGE plpgsql
AS $procedure$
 	DECLARE  
       commune_france RECORD;
	   curs_commune_france cursor for select * from ( select values::json as feat from temp_json_communes_france) as B;
	begin
		if NOT EXISTS (SELECT relname FROM pg_class WHERE relname='temp_json_communes_france') then
		
		 DROP TABLE IF EXISTS temp_json_communes_france; 
		 create temporary table temp_json_communes_france (id serial, values text) on commit drop;
		 EXECUTE format(concat('COPY temp_json_communes_france(values) FROM ''',concat(url,'\commune-france\communes.json'), ''''));
		 delete from temp_json_communes_france where id = 1;
         update temp_json_communes_france set values = SUBSTRING(values, 0, LENGTH(values));
         update temp_json_communes_france set values = SUBSTRING(values, 0, LENGTH(values)) where id = (select id from temp_json_communes_france order by id desc limit 1);
		 update temp_json_communes_france set values = replace(values,substring(values,position('"nom":"' in values) + 7, position(',' in substring(values,position('"nom":"' in values), length(values))) - 9),replace(substring(values,position('"nom":"' in values) + 7, position(',' in substring(values,position('"nom":"' in values), length(values))) - 9), '"', '\"'));
		   
		end if;
	
	 		open curs_commune_france;
			 LOOP
		        FETCH curs_commune_france INTO commune_france;
		        EXIT WHEN NOT FOUND;
		        IF commune_france.feat ->'properties'->>'insee' = insee THEN 
		            insert into "cadastre-communes"(id, geom, nom, created_at, updated_at) VALUES(commune_france.feat ->'properties'->>'insee', ST_SetSRID(ST_Multi(ST_GeomFromGeoJSON(commune_france.feat->>'geometry')), 4326), commune_france.feat->'properties' ->> 'nom', current_timestamp, current_timestamp);
		        END IF;
		     END LOOP;
		    CLOSE curs_commune_france;
	END;
$procedure$
;

CREATE OR REPLACE PROCEDURE public.add_cadastre_commune(url text)
LANGUAGE plpgsql
AS $procedure$  
    DECLARE  
       counter INTEGER := 1;   
       counter_corse INTEGER := 1;
       url_entire_communes text;  
    BEGIN  
         LOOP   
         DROP TABLE IF EXISTS temp_json_communes;          
          IF counter < 10 then         
              url_entire_communes := concat(url, '\0', counter, '\cadastre-0',counter,'-communes.json');              
              counter := counter + 1;              
          elseif counter = 20 then          
            if counter_corse = 1 then                 
                url_entire_communes := concat(url, '\2A\cadastre-2A-communes.json'); 
                counter_corse := counter_corse + 1;         
            else 
                url_entire_communes := concat(url, '\2B\cadastre-2B-communes.json'); 
                counter := counter + 1; 
             end if; 
           elsif counter = 96 then  
			     counter := 971; 
			     url_entire_communes := concat(url, '\', counter, '\cadastre-',counter,'-communes.json');    
			     counter := counter + 1;   			    
		    elsif counter = 975 then   
		         counter := counter + 1;         
		         url_entire_communes := concat(url, '\', counter, '\cadastre-',counter,'-communes.json'); 	    
			     counter := counter + 1; 	    
			 else  
			     url_entire_communes := concat(url, '\', counter, '\cadastre-',counter,'-communes.json'); 
			     counter := counter + 1;   	    
			end if;  
           create temporary table temp_json_communes (id serial PRIMARY KEY, values text not NULL);
           EXECUTE format(concat('COPY temp_json_communes(values) FROM ''',url_entire_communes, ''''));
		   delete from temp_json_communes where id = 1;
		   update temp_json_communes set values = SUBSTRING(values, 0, LENGTH(values));
	       update temp_json_communes set values = SUBSTRING(values, 0, LENGTH(values)) where id = (select id from temp_json_communes order by id desc limit 1);
		   update temp_json_communes set values = replace(values,substring(values, position('\' in values), position('"' in substring(values,position('\' in values))) - 1),'');
	       update temp_json_communes set values = replace(values,substring(values,position('"nom":"' in values) + 7, position(',' in substring(values,position('"nom":"' in values), length(values))) - 9),
	       replace(substring(values,position('"nom":"' in values) + 7, position(',' in substring(values,position('"nom":"' in values), length(values))) - 9), '"', '\"'))  where values like '%\\%'; 
	     insert into "cadastre-communes"(id, geom, nom, created_at, updated_at)
         select  feat->>'id' as id,  

			       ST_SetSRID(ST_Multi(ST_GeomFromGeoJSON(feat->>'geometry')), 4326) AS geom , 
			       
			       feat->'properties' ->> 'nom' as nom, 		
				   to_timestamp(feat->'properties' ->> 'created','YYYY-MM-DD') as created,  		                
				   to_timestamp(feat->'properties' ->> 'updated','YYYY-MM-DD') as updated   
			from ( select values::json as feat from temp_json_communes ) as A; 
		UPDATE "cadastre-communes" SET geom = ST_Multi(st_collectionextract(ST_MakeValid(geom),3)) WHERE NOT ST_IsValid(geom);	        
        EXIT WHEN counter = 977;       
       END LOOP ;   
    END;  

$procedure$;

CREATE OR REPLACE PROCEDURE public.add_cadastre_lieux_dits(url text)
 LANGUAGE plpgsql
AS $procedure$  
    DECLARE  
       counter INTEGER := 1;   
       counter_corse INTEGER := 1;
       url_entire_lieux_dits text;  
       commune_lieux_dits RECORD;
       curs_commune_lieux_dits cursor for select feat->'properties' ->> 'commune' as commune from ( select values::json as feat from temp_json_lieux_dits ) as B;	 
    BEGIN  
         LOOP   
         DROP TABLE IF EXISTS temp_json_lieux_dits;    
          IF counter < 10 then 
              url_entire_lieux_dits := concat(url, '\0', counter, '\cadastre-0',counter,'-lieux_dits.json'); 
              counter := counter + 1;              
          elseif counter = 20 then    
            if counter_corse = 1 then    
                url_entire_lieux_dits := concat(url, '\2A\cadastre-2A-lieux_dits.json'); 
                counter_corse := counter_corse + 1;    
            else 
				url_entire_lieux_dits := concat(url, '\2B\cadastre-2B-lieux_dits.json'); 
                counter := counter + 1; 
             end if; 
           elsif counter = 96 then  
			     counter := 971; 
			     url_entire_lieux_dits := concat(url, '\', counter, '\cadastre-',counter,'-lieux_dits.json'); 
			     counter := counter + 1;   
		    elsif counter = 975 then  
		         counter := counter + 1;   
			     url_entire_lieux_dits := concat(url, '\', counter, '\cadastre-',counter,'-lieux_dits.json');     
			     counter := counter + 1;  
			 else  
			     url_entire_lieux_dits := concat(url, '\', counter, '\cadastre-',counter,'-lieux_dits.json'); 
			     counter := counter + 1;   
			end if;     	
		create temporary table temp_json_lieux_dits (id serial, values text) on commit drop;  
	    EXECUTE format(concat('COPY temp_json_lieux_dits(values) FROM ''',url_entire_lieux_dits, ''''));
		delete from temp_json_lieux_dits where id = 1;
		update temp_json_lieux_dits set values = SUBSTRING(values, 0, LENGTH(values));
	    update temp_json_lieux_dits set values = SUBSTRING(values, 0, LENGTH(values)) where id = (select id from temp_json_lieux_dits order by id desc limit 1);
		 open curs_commune_lieux_dits;
		 LOOP
	        FETCH curs_commune_lieux_dits INTO commune_lieux_dits;
	        EXIT WHEN NOT FOUND;
	        IF (select count(*) from "cadastre-communes" where id = commune_lieux_dits.commune) = 0 THEN 
	           call add_commune(url, commune_lieux_dits.commune);
	        END IF;
	     END LOOP;
		 CLOSE curs_commune_lieux_dits;
	     insert into "cadastre-lieux_dits"(geom, nom, commune, created_at, updated_at)
         select ST_SetSRID(ST_GeomFromGeoJSON(feat->>'geometry'), 4326) AS geom , 
			       feat->'properties' ->> 'nom' as nom, 	
			       feat->'properties' ->> 'commune' as commune,
				   to_timestamp(feat->'properties' ->> 'created','YYYY-MM-DD') as created,  		                
				   to_timestamp(feat->'properties' ->> 'updated','YYYY-MM-DD') as updated   
		from ( select values::json as feat from temp_json_lieux_dits ) as B; 
        EXIT WHEN counter = 977;      
       END LOOP ;   
    END;  
$procedure$
;

CREATE OR REPLACE PROCEDURE public.add_cadastre_parcelles(url text)
LANGUAGE plpgsql
AS $procedure$  
    DECLARE  
       counter INTEGER := 1;   
       counter_corse INTEGER := 1;
       url_entire_parcelles text;  
       commune_parcelles RECORD;
	   curs_commune_parcelles cursor for select feat->'properties' ->> 'commune' as commune from ( select values::json as feat from temp_json_parcelles limit 1000) as B;
    BEGIN  
         LOOP   
           DROP TABLE IF EXISTS temp_json_parcelles;          
          IF counter < 10 then      
              url_entire_parcelles := concat(url, '\0', counter, '\cadastre-0',counter,'-parcelles.json');        
              counter := counter + 1;             
          elseif counter = 20 then          
            if counter_corse = 1 then             
                url_entire_parcelles := concat(url, '\2A\cadastre-2A-parcelles.json'); 
                counter_corse := counter_corse + 1;             
            else 
                url_entire_parcelles := concat(url, '\2B\cadastre-2B-parcelles.json');              
                counter := counter + 1; 
             end if; 
           elsif counter = 96 then  
			     counter := 971; 			
			     url_entire_parcelles := concat(url, '\', counter, '\cadastre-',counter,'-parcelles.json'); 			    
			     counter := counter + 1;   			    
		    elsif counter = 975 then  		    
		         counter := counter + 1;   		        
			     url_entire_parcelles := concat(url, '\', counter, '\cadastre-',counter,'-parcelles.json'); 			    
			     counter := counter + 1; 			    
			 else  			
			     url_entire_parcelles := concat(url, '\', counter, '\cadastre-',counter,'-parcelles.json'); 			
			     counter := counter + 1;   			    
			end if;      
		create temporary table temp_json_parcelles (id serial, values text) on commit drop;    
		EXECUTE format(concat('COPY temp_json_parcelles(values) FROM ''',url_entire_parcelles, ''''));
	    delete from temp_json_parcelles where id = 1;
	    update temp_json_parcelles set values = SUBSTRING(values, 0, LENGTH(values));
	    update temp_json_parcelles set values = SUBSTRING(values, 0, LENGTH(values)) where id = (select id from temp_json_parcelles order by id desc limit 1);  
	      insert into "cadastre-parcelles"(id, geom, commune, prefixe, "section", numero, contenance, created_at, updated_at)
 		select  feat->>'id' as id,
     		   ST_SetSRID(ST_GeomFromGeoJSON(feat->>'geometry'), 4326) AS geom , 
		       feat->'properties' ->> 'commune' as commune,
		       feat->'properties' ->> 'prefixe' as prefixe,
		       feat->'properties' ->> 'section' as "section",
		       feat->'properties' ->> 'numero' as numero,
		       cast(feat->'properties' ->> 'contenance' as double precision) as contenance,
			   to_timestamp(feat->'properties' ->> 'created','YYYY-MM-DD') as created,  		                
			   to_timestamp(feat->'properties' ->> 'updated','YYYY-MM-DD') as updated   
		from ( select values::json as feat from temp_json_parcelles limit 1000) as B; 			        
        EXIT WHEN counter = 977;         
       END LOOP ;   
    END;  
$procedure$
;

CREATE OR REPLACE PROCEDURE public.verif_geom()
LANGUAGE plpgsql
AS $procedure$ 
declare 
    curs_geom cursor for select id, geom from tbplotsold as t; 
    geomRecord RECORD; 
begin     
    open curs_geom; 
    loop 
       fetch curs_geom into geomRecord;        
       update "tbplotsold" set geom = ST_Multi(st_collectionextract(ST_MakeValid(geom),3)) where not ST_IsValid(geomRecord.geom) and id = geomRecord.id;
    end loop; 
    close curs_geom; 
end;
$procedure$
;

CREATE OR REPLACE PROCEDURE public.add_superficie()
LANGUAGE plpgsql
AS $procedure$
declare
	curs_superficie_2017 cursor for select sc.insee as idcommune, sc.superficie as sup from superficie_communes_2017_07 as sc order by idcommune desc;
	curs_superficie_2018 cursor for select sc.insee as idcommune, sc.superficie as sup from superficie_communes_2018_07 as sc order by idcommune desc;
	superficie_2017_record RECORD;
	superficie_2018_record RECORD;
	new_insee text;
begin
	-- Insert data from 2017 table	
	open curs_superficie_2017;
	loop	
		fetch curs_superficie_2017 into superficie_2017_record;
		EXIT WHEN NOT FOUND;	
		new_insee := concat(substring(superficie_2017_record.idcommune, 1, 2), substring(superficie_2017_record.idcommune, 4, 6));
		if (select count(id) from communes where id = new_insee) != 0 then
				insert into superficie(insee, superficie, year) values(new_insee, superficie_2017_record.sup, '2017');
		end if;
	end loop;
	close curs_superficie_2017;
	-- Insert data from 2018 table
	open curs_superficie_2018;
	loop		
		fetch curs_superficie_2018 into superficie_2018_record;		
		EXIT WHEN NOT FOUND;		
		new_insee := concat(substring(superficie_2018_record.idcommune, 1, 2), substring(superficie_2018_record.idcommune, 4, 6));	
		if (select count(id) from communes where id = new_insee) != 0 then
				insert into superficie(insee, superficie, year) values(new_insee, superficie_2018_record.sup, '2018');
		end if;
	end loop;
	close curs_superficie_2018;
end;
$procedure$
;

CREATE OR REPLACE PROCEDURE public.add_stats()
 LANGUAGE plpgsql
AS $procedure$
declare
	curs_stats2009 cursor for select * from stats2009old;
	curs_stats2010 cursor for select * from stats2010old;
	curs_stats2011 cursor for select * from stats2011old;
	curs_stats2012 cursor for select * from stats2012old;
	curs_stats2013 cursor for select * from stats2013old;
	curs_stats2014 cursor for select * from stats2014old;
	curs_stats2015 cursor for select * from stats2015old;
	curs_stats2016 cursor for select * from stats2016old;
	curs_stats2017 cursor for select * from product_wines_departement_2017;
	curs_stats2018 cursor for select * from product_wines_departement_2018;
	stats2009_record RECORD;
	stats2010_record RECORD;
	stats2011_record RECORD;
	stats2012_record RECORD;
	stats2013_record RECORD;
	stats2014_record RECORD;
	stats2015_record RECORD;
	stats2016_record RECORD;
	stats2017_record RECORD;
	stats2018_record RECORD;
begin	
	-- Insert stats2009 data	
	open curs_stats2009;
	loop
    	fetch curs_stats2009 into stats2009_record;	
		EXIT when not found;	
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aoc_superficie, vdqs_superficie, apte_cognac_armagnac_superficie, autre_superficie, blanc_aoc_quantite, rouge_rose_aoc_quantite, blanc_vdqs_quantite, rouge_rose_quantite, apte_cognac_armagnac, blanc_pays, rouge_rose_pays, blanc_autre, rouge_rose_autre, blanc_quantite_totale, rouge_rose_quantite_totale, total, annee)
		select stats2009_record.departid, stats2009_record.departname, stats2009_record.nbdeclarations, stats2009_record.superficietotale, stats2009_record.aoc_sup, stats2009_record.vdqs_sup, stats2009_record.aptecognacarmagnac_sup, stats2009_record.autre_sup, stats2009_record.vinsblancs_aoc_quant, stats2009_record.rougesouroses_aoc_quant, stats2009_record.vinsblancs_vdqs_quant, stats2009_record.rougesouroses_vdqs_quant, stats2009_record.aptecognacarmagnac_quant, stats2009_record.blancspays, stats2009_record.rougesourosespays, stats2009_record.blancsautres_quant, stats2009_record.rougesouroses_quant, stats2009_record.blancs_quanttotale, stats2009_record.rougesouroses_quanttotale, stats2009_record.total, '2009';	
	end loop;
	close curs_stats2009;
	-- Insert stats2010 data	
	open curs_stats2010;
	loop
		fetch curs_stats2010 into stats2010_record;	
		EXIT when not found;
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aop_superficie, igp_superficie, apte_cognac_armagnac_superficie, autre_superficie, blanc_aop_quantite, rouge_rose_aop_quantite, blanc_igp_quantite, rouge_rose_quantite, apte_cognac_armagnac, blanc_pays, rouge_rose_pays, blanc_autre, rouge_rose_autre, blanc_quantite_totale, rouge_rose_quantite_totale, total, annee)
		select stats2010_record.departid, stats2010_record.departname, stats2010_record.nbdeclarations, stats2010_record.superficietotale, stats2010_record.aop_sup, stats2010_record.vdqs_sup, stats2010_record.aptecognacarmagnac_sup, stats2010_record.autre_sup, stats2010_record.vinsblancs_aop_quant, stats2010_record.rougesouroses_aop_quant, stats2010_record.vinsblancs_igp_quant, stats2010_record.rougesouroses_igp_quant, stats2010_record.aptecognacarmagnac_quant, stats2010_record.blancspays, stats2010_record.rougesourosespays, stats2010_record.blancsautres_quant, stats2010_record.rougesouroses_quant, stats2010_record.blancs_quanttotale, stats2010_record.rougesouroses_quanttotale, stats2010_record.total, '2010';
	end loop;
	close curs_stats2010;
	-- Insert stats2011 data	
	open curs_stats2011;
	loop	
		fetch curs_stats2011 into stats2011_record;	
		EXIT when not found;	
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aop_superficie, apte_cognac_armagnac_superficie, autre_superficie, blanc_aop_quantite, rouge_rose_aop_quantite, blanc_igp_quantite, rouge_rose_quantite, blanc_autre, rouge_rose_autre, blanc_quantite_totale, rouge_rose_quantite_totale, total, annee)
		select stats2011_record.departid, stats2011_record.departname, stats2011_record.nbdeclarations, stats2011_record.superficietotale, stats2011_record.aop_sup, stats2011_record.aptecognacarmagnac_sup, stats2011_record.autre_sup, stats2011_record.vinsblancs_aop_quant, stats2011_record.rougesouroses_aop_quant, stats2011_record.vinsblancs_igp_quant, stats2011_record.rougesouroses_igp_quant, stats2011_record.blancsautres_quant, stats2011_record.rougesouroses_quant, stats2011_record.blancs_quanttotale, stats2011_record.rougesouroses_quanttotale, stats2011_record.total, '2011';	
	end loop;
	close curs_stats2011;
	-- Insert stats2012 data	
	open curs_stats2012;
	loop	
		fetch curs_stats2012 into stats2012_record;	
		EXIT when not found;	
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aop_superficie, apte_cognac_armagnac_superficie, igp_superficie, vsig_superficie, blanc_aop_quantite, rouge_aop_quantite, rose_aop_quantite, blanc_igp_quantite, rouge_igp_quantite, rose_igp_quantite, blanc_vsig_quantite, rouge_vsig_quantite, rose_vsig_quantite, blanc_quantite_totale, rouge_quantite_totale, rose_quantite_totale, total, annee)
		select stats2012_record.departid, stats2012_record.departname, stats2012_record.nbdeclarations, stats2012_record.superficietotale, stats2012_record.aop_sup, stats2012_record.aptecognacarmagnac_sup, stats2012_record.igp_sup, stats2012_record.vsig_sup, stats2012_record.vinsblancs_aop_quant, stats2012_record.rouges_aop_quant, stats2012_record.roses_aop_quant, stats2012_record.vinsblancs_igp_quant, stats2012_record.rouges_igp_quant, stats2012_record.roses_igp_quant, stats2012_record.vinblanc_vsig_quant, stats2012_record.rouge_vsig_quant, stats2012_record.rose_vsig_quant,stats2012_record.blanctotal, stats2012_record.rougetotal, stats2012_record.rosetotal, stats2012_record.total, '2012';
	end loop;
	close curs_stats2012;
	-- Insert stats2013 data	
	open curs_stats2013;
	loop
		fetch curs_stats2013 into stats2013_record;	
		EXIT when not found;	
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aop_superficie, apte_cognac_armagnac_superficie, igp_superficie, vsig_superficie, blanc_aop_quantite, rouge_aop_quantite, rose_aop_quantite, blanc_igp_quantite, rouge_igp_quantite, rose_igp_quantite, blanc_vsig_quantite, rouge_vsig_quantite, rose_vsig_quantite, blanc_quantite_totale, rouge_quantite_totale, rose_quantite_totale, apte_cognac_armagnac, non_commercialisable, total, annee)
		select stats2013_record.departid, stats2013_record.departname, stats2013_record.nbdeclarations, stats2013_record.superficietotale, stats2013_record.aop_sup, stats2013_record.aptecognacarmagnac_sup, stats2013_record.igp_sup, stats2013_record.vsig_sup, stats2013_record.vinsblancs_aop_quant, stats2013_record.rouges_aop_quant, stats2013_record.roses_aop_quant, stats2013_record.vinsblancs_igp_quant, stats2013_record.rouges_igp_quant, stats2013_record.roses_igp_quant, stats2013_record.vinblanc_vsig_quant, stats2013_record.rouge_vsig_quant, stats2013_record.rose_vsig_quant,stats2013_record.blanctotal, stats2013_record.rougetotal, stats2013_record.rosetotal, stats2013_record.aptecognacarmagnac, stats2013_record.noncommercialisable, stats2013_record.total, '2013';	
	end loop;
	close curs_stats2013;
	-- Insert stats2014 data	
	open curs_stats2014;
	loop	
		fetch curs_stats2014 into stats2014_record;	
		EXIT when not found;	
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aop_superficie, apte_cognac_armagnac_superficie, igp_superficie, vsig_superficie, blanc_aop_quantite, rouge_aop_quantite, rose_aop_quantite, vsi_vci_aop_quantite, blanc_igp_quantite, rouge_igp_quantite, rose_igp_quantite, blanc_vsig_quantite, rouge_vsig_quantite, rose_vsig_quantite, apte_cognac_armagnac, commercialisable, non_commercialisable, total, annee)
		select stats2014_record.departid, stats2014_record.departname, stats2014_record.nbdeclarations, stats2014_record.superficietotale, stats2014_record.aop_sup, stats2014_record.aptecognacarmagnac_sup, stats2014_record.igp_sup, stats2014_record.vsig_sup, stats2014_record.vinsblancs_aop_quant, stats2014_record.rouges_aop_quant, stats2014_record.roses_aop_quant, stats2014_record.vsi_vci_aop_quant, stats2014_record.vinsblancs_igp_quant, stats2014_record.rouges_igp_quant, stats2014_record.roses_igp_quant, stats2014_record.vinblanc_vsig_quant, stats2014_record.rouge_vsig_quant, stats2014_record.rose_vsig_quant, stats2014_record.aptecognacarmagnac, stats2014_record.commercialisable, stats2014_record.noncommercialisable, stats2014_record.total, '2014';
	end loop;
	close curs_stats2014;
	-- Insert stats2015 data	
	open curs_stats2015;
	loop	
		fetch curs_stats2015 into stats2015_record;	
		EXIT when not found;	
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aop_superficie, apte_cognac_armagnac_superficie, igp_superficie, vsig_superficie, blanc_aop_quantite, rouge_aop_quantite, rose_aop_quantite, vsi_vci_aop_quantite, blanc_igp_quantite, rouge_igp_quantite, rose_igp_quantite, blanc_vsig_quantite, rouge_vsig_quantite, rose_vsig_quantite, apte_cognac_armagnac, commercialisable, non_commercialisable, total, annee)
		select stats2015_record.departid, stats2015_record.departname, stats2015_record.nbdeclarations, stats2015_record.superficietotale, stats2015_record.aop_sup, stats2015_record.aptecognacarmagnac_sup, stats2015_record.igp_sup, stats2015_record.vsig_sup, stats2015_record.vinsblancs_aop_quant, stats2015_record.rouges_aop_quant, stats2015_record.roses_aop_quant, stats2015_record.vsi_vci_aop_quant, stats2015_record.vinsblancs_igp_quant, stats2015_record.rouges_igp_quant, stats2015_record.roses_igp_quant, stats2015_record.vinblanc_vsig_quant, stats2015_record.rouge_vsig_quant, stats2015_record.rose_vsig_quant, stats2015_record.aptecognacarmagnac, stats2015_record.commercialisable, stats2015_record.noncommercialisable, stats2015_record.total, '2015';	
	end loop;
	close curs_stats2015;
	-- Insert stats2016 data	
	open curs_stats2016;
	loop	
		fetch curs_stats2016 into stats2016_record;	
		EXIT when not found;	
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aop_superficie, apte_cognac_armagnac_superficie, igp_superficie, vsig_superficie, blanc_aop_quantite, rouge_aop_quantite, rose_aop_quantite, vsi_vci_aop_quantite, blanc_igp_quantite, rouge_igp_quantite, rose_igp_quantite, blanc_vsig_quantite, rouge_vsig_quantite, rose_vsig_quantite, apte_cognac_armagnac, commercialisable, non_commercialisable, total, annee)
		select stats2016_record.departid, stats2016_record.departname, stats2016_record.nbdeclarations, stats2016_record.superficietotale, stats2016_record.aop_sup, stats2016_record.aptecognacarmagnac_sup, stats2016_record.igp_sup, stats2016_record.vsig_sup, stats2016_record.vinsblancs_aop_quant, stats2016_record.rouges_aop_quant, stats2016_record.roses_aop_quant, stats2016_record.vsi_vci_aop_quant, stats2016_record.vinsblancs_igp_quant, stats2016_record.rouges_igp_quant, stats2016_record.roses_igp_quant, stats2016_record.vinblanc_vsig_quant, stats2016_record.rouge_vsig_quant, stats2016_record.rose_vsig_quant, stats2016_record.aptecognacarmagnac, stats2016_record.commercialisable, stats2016_record.noncommercialisable, stats2016_record.total, '2016';	
	end loop;
	close curs_stats2016;
	-- Insert stats2017 data	
	open curs_stats2017;
	loop	
		fetch curs_stats2017 into stats2017_record;
		EXIT when not found;
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aop_superficie, apte_cognac_armagnac_superficie, igp_superficie, vsig_superficie, blanc_aop_quantite, rouge_aop_quantite, rose_aop_quantite, vsi_vci_aop_quantite, blanc_igp_quantite, rouge_igp_quantite, rose_igp_quantite, blanc_vsig_quantite, rouge_vsig_quantite, rose_vsig_quantite, apte_cognac_armagnac, commercialisable, autre_prod, total, annee)
		select stats2017_record.departid, stats2017_record.departname, stats2017_record.nbdeclarations, stats2017_record.superficietotale, stats2017_record.aop_sup, stats2017_record.aptecognacarmagnac_sup, stats2017_record.igp_sup, stats2017_record.vsig_sup, stats2017_record.vinsblancs_aop_quant, stats2017_record.rouges_aop_quant, stats2017_record.roses_aop_quant, stats2017_record.vsi_vci_aop_quant, stats2017_record.vinsblancs_igp_quant, stats2017_record.rouges_igp_quant, stats2017_record.rose_igp_quantvci_igp_quant, stats2017_record.vinblanc_vsig_quant, stats2017_record.rouge_vsig_quant, stats2017_record.rose_vsig_quant, stats2017_record.aptecognacarmagnac, stats2017_record.commercialisable, stats2017_record.autreproduction, stats2017_record.total, '2017';
	
	end loop;
	close curs_stats2017;
	-- Insert stats2018 data	
	open curs_stats2018;
	loop	
		fetch curs_stats2018 into stats2018_record;	
		EXIT when not found;	
		insert into stats_product (id_departement, departname, nbdeclarations, superficie_totale, aop_superficie, apte_cognac_armagnac_superficie, igp_superficie, vsig_superficie, blanc_aop_quantite, rouge_aop_quantite, rose_aop_quantite, vsi_vci_aop_quantite, blanc_igp_quantite, rouge_igp_quantite, rose_igp_quantite, blanc_vsig_quantite, rouge_vsig_quantite, rose_vsig_quantite, apte_cognac_armagnac, commercialisable, autre_prod, total, annee)
		select stats2018_record.departid, stats2018_record.departname, stats2018_record.nbdeclarations, stats2018_record.superficietotale, stats2018_record.aop_sup, stats2018_record.aptecognacarmagnac_sup, stats2018_record.igp_sup, stats2018_record.vsig_sup, stats2018_record.vinsblancs_aop_quant, stats2018_record.rouges_aop_quant, stats2018_record.roses_aop_quant, stats2018_record.vsi_vci_aop_quant, stats2018_record.vinsblancs_igp_quant, stats2018_record.rouges_igp_quant, stats2018_record.rose_igp_quantvci_igp_quant, stats2018_record.vinblanc_vsig_quant, stats2018_record.rouge_vsig_quant, stats2018_record.rose_vsig_quant, stats2018_record.aptecognacarmagnac, stats2018_record.commercialisable, stats2018_record.autreproduction, stats2018_record.total, '2018';	
	end loop;
	close curs_stats2018;
END;
$procedure$
;
```
